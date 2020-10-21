---
title: parse 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの parse 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a942015908c9608a76d3c49c411de9d17d6e70f5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248610"
---
# <a name="parse-operator"></a>parse 演算子

文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 解析が失敗した文字列の場合、計算列には null が含まれます。
詳細については、「 [parse」演算子](parsewhereoperator.md)を参照してください。

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>構文

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ]*式* `with` `*` (*stringconstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

## <a name="arguments"></a>引数

* *T*: 入力テーブル。
* kind:

    * simple (既定値): StringConstant は通常の文字列値で、一致は strict です。 すべての文字列の区切り記号が解析された文字列に含まれ、すべての拡張列が必要な型と一致している必要があります。
        
    * regex: StringConstant は正規表現にすることができ、一致は厳格です。 このモードの regex として使用できるすべての文字列区切り記号は、解析された文字列に含まれる必要があり、すべての拡張列は必要な型と一致する必要があります。
    
    * flags: `U` RE2 フラグで、(最短一致)、 `m` (複数行モード)、( `s` 新しい行と一致)、 `\n` `i` (大文字と小文字を区別[RE2 flags](re2.md)しない) のような regex モードで使用されるフラグ。
        
    * 緩やか: StringConstant は通常の文字列値で、一致は緩和されます。 解析された文字列には、すべての文字列の区切り記号が表示されますが、拡張列は必要な型と部分的に一致する場合があります。 必要な型と一致しない拡張列では、値 null が返されます。

* *式*: 文字列に評価される式。

* *ColumnName:* 文字列式から抽出された値を代入する列の名前。 
  
* *ColumnType:* Optional. 値の変換後の型を示すスカラー値。 既定値は `string` 型です。

## <a name="returns"></a>戻り値

入力テーブル。演算子に提供される列の一覧に従って拡張されます。

**ヒント**

* [`project`](projectoperator.md)一部の列を削除したり、名前を変更したりする場合は、を使用します。

* 迷惑メールの値をスキップするには、パターンで * を使用します。 

    > [!NOTE] 
    > `*`型の列の後にを使用することはできません `string` 。

* 解析パターンは、*文字列定数*だけでなく、 *ColumnName*で始めることができます。

* 解析された *式* が型でない場合は `string` 、型に変換され `string` ます。

* Regex モードが使用されている場合、regex フラグを追加して、解析で使用される regex 全体を制御するオプションがあります。

* Regex モードでは、解析によってパターンが正規表現に変換されます。 [RE2 構文](re2.md)を使用して照合を実行し、内部で処理される番号付きのキャプチャグループを使用します。
    次に例を示します。

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Parse ステートメントでは、解析によって内部的に生成される regex は `.*?<regex1>(.*?)<regex2>(\-\d+)` です。
        
    * `*` はに変換されました `.*?` 。
        
    * `string` はに変換されました `.*?` 。
        
    * `long` はに変換されました `\-\d+` 。

## <a name="examples"></a>例

演算子を使用すると、 `parse` `extend` 同じ式で複数のアプリケーションを使用して、テーブルに効率的にテーブルを提供 `extract` `string` できます。 この結果は、テーブルに、 `string` 個別の列に分割する複数の値を含む列がある場合に便利です。 たとえば、開発者のトレース (" `printf` "/"") ステートメントによって生成された列など `Console.WriteLine` です。

次の例では、テーブルの列にという `EventText` `Traces` 形式の文字列が含まれているとし `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` ます。
この操作により、、、、、、、、およびの6つの列を含むテーブルが拡張され `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previousLockTime` `Month` `Day` ます。 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|前の Locktime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40: 00.0000000|2016-02-17 08:39: 00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|

**Regex モードの場合**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|前の Locktime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00、 |02/17/2016 08:40:00、 |2016-02-17 08:39: 00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01、 |02/17/2016 08:40:01、 |2016-02-17 08:39: 01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01、 |02/17/2016 08:40:01、 |2016-02-17 08:39: 01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00、 |02/17/2016 08:41:00、 |2016-02-17 08:40: 00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01、 |02/17/2016 08:41:00、 |2016-02-17 08:40: 01.0000000|

**Regex のフラグを使用する regex モードの場合**

このクエリは、次のクエリを使用してのみ実行することをお勧めします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler、totalSlices = 27、sliceNumber = 23、lockTime = 02/17/2016 08:40:01、releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler、totalSlices = 27、sliceNumber = 15、lockTime = 02/17/2016 08:40:00、releaseTime = 02/17/2016 08:40:00|
|PipelineScheduler、totalSlices = 27、sliceNumber = 20、lockTime = 02/17/2016 08:40:01、releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler、totalSlices = 27、sliceNumber = 22、lockTime = 02/17/2016 08:41:01、releaseTime = 02/17/2016 08:41:00|
|PipelineScheduler、totalSlices = 27、sliceNumber = 16、lockTime = 02/17/2016 08:41:00、releaseTime = 02/17/2016 08:41:00|

既定のモードは最長一致であるため、期待される結果が得られません。
いくつかのレコードがあり、その場所が小文字で、場合によっ *ては大文字*  として表示されることがありますが、一部の値に対して null が返されることがあります。

必要な結果を得るには、最短一致のを使用してクエリを実行 `U` し、大文字と小文字を区別する regex フラグを無効にし `i` ます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|

解析された文字列に改行がある場合は、フラグを使用し `s` てテキストを解析します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|totalSlices|lockTime|releaseTime|前の Locktime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40: 00.0000000|2016-02-17 08:40: 00.0000000|2016-02-17 08:39: 00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40: 01.0000000|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40: 01.0000000|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41: 00.0000000|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41: 01.0000000|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|

**緩やかモード**

この緩やかモードの例では、 *Totalslices* 拡張列を型にする必要があり `long` ます。 ただし、解析された文字列では、値が " *Nonvalidlongvalue*" になっています。
*Releasetime*拡張列では、値*nonvaliddatetime*を*datetime*として解析できません。
この2つの拡張列では、値が null になりますが、 *sliceNumber*などの他の列は正しい値を取得します。

以下の同じクエリに対して option *kind = simple* を使用すると、すべての拡張列に対して null が返されます。 このオプションは拡張列に対して厳密であり、緩やかモードと単純モードの違いがあります。

 > [!NOTE] 
 > 緩やかモードでは、拡張列を部分的に一致させることができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|前の Locktime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39: 00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39: 01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39: 01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|
 
