---
title: タイム ウィンドウ内での参加 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのタイム ウィンドウ内での参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa3b81694714ef5af94407cdfdac263af0631e40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513364"
---
# <a name="joining-within-time-window"></a>タイム ウィンドウ内での結合

大きなデータ セット間で、操作 ID やセッション ID などの大きなデータ セットを結合し、左側と右側の列の間`$right``$left``datetime`の "時間距離" に制限を追加して、各左側のレコード ( ) に対して照合する必要がある右側の ( ) レコードをさらに制限する場合に便利です。 これは通常のKusto結合操作とは異なり、"等結合"部分(高基数キーまたは左右のデータセットに一致する)に加えて、距離関数を適用し、それを使用してかなり高速に結合することができます。 距離関数は等値とは違います (つまり、両方`dist(x,y)``dist(y,z)`とも真の場合は、その`dist(x,z)`関数も同様です)。*内部的には、これを「斜め結合」と呼ぶことがあります。*

たとえば、比較的小さい時間枠内でイベント シーケンスを識別するとします。 この例を示すために、次のスキーマを`T`持つテーブルがあるとします。

- `SessionId`: 相関 ID`string`を持つ型の列。
- `EventType`: レコードのイベント`string`タイプを識別する型の列。
- `Timestamp`: レコードによって記述`datetime`されたイベントがいつ発生したのか示す型の列。

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
|0|A|2017-10-01 00:00:00.0000000|
|0|B|2017-10-01 00:01:00.0000000|
|1|B|2017-10-01 00:02:00.0000000|
|1|A|2017-10-01 00:03:00.0000000|
|3|A|2017-10-01 00:04:00.0000000|
|3|B|2017-10-01 00:10:00.0000000|


**問題の説明**

クエリで次の質問に回答する必要があります。

   イベントタイプの後にイベントタイプ`A`が続いたすべてのセッションIDを、時間枠`B`内で`1min`検索します。

(上記のサンプル データでは、このようなセッション ID`0`は .

意味的には、次のクエリは、非効率的ではあるが、この質問に答えます。

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

|SessionId|[開始]|End|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

このクエリを最適化するには、以下で説明するように、タイム ウィンドウを結合キーとして表すように書き直します。

**タイム ウィンドウを考慮したクエリの書き換え**

この考え方は、`datetime`値が時間枠の半分のサイズであるバケットに「分離」されるようにクエリを書き換えます。
その後、Kusto の等結合を使用して、これらのバケット ID を比較できます。

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

|SessionId|[開始]|End|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**実行可能なクエリ参照 (テーブルがインライン化された場合)**

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

|SessionId|[開始]|End|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**50M データ クエリ**

次のクエリは、50M レコードと約 10M ID のデータ セットをエミュレートし、上記の手法を使用してクエリを実行します。

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