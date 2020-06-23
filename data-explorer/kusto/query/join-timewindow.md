---
title: 時間枠内での参加-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの時間枠内での参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d23d76fdcc592435a8ec7fa24ef5d0dfd5186c68
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128466"
---
# <a name="time-window-join"></a>時間枠内での結合

多くの場合、大規模なカーディナリティキー (操作 ID やセッション ID など) で2つの大きなデータセットを結合し、左側の `$right` `$left` 列と右側の列の間に "時間の距離" の制限を追加することで、各左側の () レコードに対して一致する必要がある右側の () レコードを制限し `datetime` ます。 これは、通常の Kusto 結合操作とは異なり、"等結合" 部分 (高カーディナリティキーまたは左右のデータセットに一致) に加えて、システムは distance 関数を適用し、それを使用して結合の速度を大幅に向上させることもできます。 Distance 関数は等値として動作しないことに注意してください (つまり、との両方が true の場合は、 `dist(x,y)` `dist(y,z)` これも true になりません `dist(x,z)` )。*内部的には、これを "対角線結合" と呼ぶことがあります。*

たとえば、比較的小さな時間枠でイベントシーケンスを識別するとします。 この例を示すために、次のスキーマを持つテーブルがあるとし `T` ます。

- `SessionId`: `string` 相関 id を持つ型の列。
- `EventType`: `string` レコードのイベントの種類を識別する型の列。
- `Timestamp`: `datetime` レコードで記述されたイベントが発生した日時を示す型の列。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionId|EventType|Timestamp|
|---|---|---|
|0|A|2017-10-01 00:00: 00.0000000|
|0|B|2017-10-01 00:01: 00.0000000|
|1|B|2017-10-01 00:02: 00.0000000|
|1|A|2017-10-01 00:03: 00.0000000|
|3|A|2017-10-01 00:04: 00.0000000|
|3|B|2017-10-01 00:10: 00.0000000|


**問題の説明**

クエリで次の質問に答える必要があります。

   イベントの種類の `A` 後に時間枠でイベントの種類が含まれていたすべてのセッション id を検索し `B` `1min` ます。

(上記のサンプルデータでは、このようなセッション ID のみが `0` です)。

意味的には、次のクエリはこの質問に対して効率が悪いと答えます。

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionId|開始|End|
|---|---|---|
|0|2017-10-01 00:00: 00.0000000|2017-10-01 00:01: 00.0000000|

このクエリを最適化するには、時間枠が結合キーとして表されるように、以下に説明するように書き直してください。

**時間枠を考慮してクエリを書き直しています**

その目的は、 `datetime` 値が時間枠の半分のサイズであるバケットに値が "分離" されるように、クエリを書き直しておくことです。
Kusto の等結合を使用して、これらのバケット Id を比較できます。

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|開始|End|
|---|---|---|
|0|2017-10-01 00:00: 00.0000000|2017-10-01 00:01: 00.0000000|

**実行可能なクエリ参照 (テーブルインライン)**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|開始|End|
|---|---|---|
|0|2017-10-01 00:00: 00.0000000|2017-10-01 00:01: 00.0000000|


**50M data クエリ**

次のクエリでは、50M レコードのデータセットと ~ 1000 の Id をエミュレートし、前述の手法でクエリを実行します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|Count|
|---|
|1276|
