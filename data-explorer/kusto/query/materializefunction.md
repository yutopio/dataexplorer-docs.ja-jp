---
title: 具体化 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの具体化 () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: 0e08857e01ffa3da1bcb23d16d1df4908336f76f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346866"
---
# <a name="materialize"></a>materialize()

他のサブクエリが部分的な結果を参照できるように、クエリの実行時にサブクエリの結果をキャッシュできます。
 
## <a name="syntax"></a>構文

`materialize(`*expression*`)`

## <a name="arguments"></a>引数

* *式*: クエリの実行中に評価およびキャッシュされる表形式の式。

> [!NOTE]
> 具体化のキャッシュサイズは**5 GB**です。 この制限はクラスターノードごとに行われ、同時に実行されるすべてのクエリに共通です。 クエリでが使用され `materialize()` ていて、それ以上のデータをキャッシュが保持できない場合、クエリはエラーで中止されます。

>[!TIP]
>
>* 具体化されたデータセットを減らしてクエリのセマンティクスを保持する、可能なすべての演算子をプッシュします。 たとえば、フィルターを使用するか、必要な列のみを射影します。
>* オペランドに1回だけ実行できる相互サブクエリがある場合は、結合または共用体で具体化を使用します。 たとえば、結合/共用フォーク脚です。 「 [Join 演算子の使用例」を](#examples-of-query-performance-improvement)参照してください。
>* 具現化は、キャッシュされた結果に名前を指定した場合にのみ、let ステートメントで使用できます。 [Let ステートメントの使用例を](#examples-of-using-materialize)参照してください。

## <a name="examples-of-query-performance-improvement"></a>クエリパフォーマンスの向上の例

次の例では、を `materialize()` 使用してクエリのパフォーマンスを向上させる方法を示します。
式 `_detailed_data` は関数を使用して定義され `materialize()` ているため、1回だけ計算されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|State|EventType|EventPercentage|events|
|---|---|---|---|
|ハワイ WATERS|Waterspout|100|2|
|レイクオンタリオ|海上雷雨風|100|8|
|アラスカ州 GULF|Waterspout|100|4|
|大西洋北部|海上雷雨風|95.2127659574468|179|
|レイク ERIE|海上雷雨風|92.5925925925926|25|
|E 太平洋|Waterspout|90|9|
|レイクミシガン|海上雷雨風|85.1648351648352|155|
|レイク HURON|海上雷雨風|79.3650793650794|50|
|メキシコの GULF|海上雷雨風|71.7504332755633|414|
|ハワイ|高サーフィン|70.0218818380744|320|


次の例では、乱数のセットを生成し、を計算します。 
* set 内の個別の値の数 ( `Dcount` )
* セット内の上位3つの値 
* セット内のこれらのすべての値の合計 
 
この操作は[バッチ](batches.md)と具体化を使用して行うことができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

結果セット 1:  

|Update|
|---|
|2578351|

結果セット 2: 

|value|
|---|
|9999998|
|9999998|
|9999997|

結果セット 3: 

|SUM|
|---|
|15002960543563|

## <a name="examples-of-using-materialize"></a>具体化 () の使用例

> [!TIP]
> ほとんどのクエリでは、大量の行にわたって動的オブジェクトからフィールドを抽出する場合、インジェスト時に列を具体化します。

複数回使用する値を含むステートメントを使用するには `let` 、[具体化 () 関数](./materializefunction.md)を使用します。 具体化されたデータセットを減らすことができるすべての演算子をプッシュし、引き続きクエリのセマンティクスを維持します。 たとえば、フィルターを使用するか、必要な列のみを射影します。

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
```

に対するフィルター `Text` は相互に適用され、具体化式にプッシュできます。
クエリに必要なのは、列 `Timestamp` 、、 `Text` `Resource1` 、およびだけ `Resource2` です。 具体化された式の中でこれらの列を射影します。
    
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
```
    
フィルターが同じでない場合は、次のクエリのようになります。  

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
 ```

結合フィルターが具体化された結果を劇的に減らす場合は、次のクエリのように、具体化された結果に対する両方のフィルターを論理式で結合し `or` ます。 ただし、クエリのセマンティクスを維持するために、各共用体区間にフィルターを保持します。
     
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
```
    
