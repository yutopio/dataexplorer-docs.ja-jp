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
ms.openlocfilehash: 07318a64781678410374f902ff8fe5514a4bdd17
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071906"
---
# <a name="parse-operator"></a>parse 演算子

文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 解析が失敗した文字列の場合、計算列には null が含まれます。 

解析[に](parsewhereoperator.md)失敗した文字列を除外する parse 演算子を参照してください。

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**構文**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ]*式* `with` `*` (*stringconstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**引数**

* *T*: 入力テーブル。
* kind: 

    * simple (既定値): StringConstant は通常の文字列値で、一致は厳密です。これは、すべての文字列区切り記号を解析された文字列に含める必要があり、すべての拡張列が必要な型と一致する必要があることを意味します。
        
    * regex: StringConstant は正規表現にすることができ、一致は厳密です。これは、すべての文字列区切り記号 (このモードの regex) が解析された文字列に含まれ、すべての拡張列が必要な型と一致する必要があることを意味します。
    
    * flags: regex モードで使用されるフラグ `U` (非最長一致)、 `m` (複数行モード)、 `s` (新しい行に一致 `\n` )、 `i` (大文字と小文字を区別しない)、 [RE2 フラグ](re2.md)。
        
    * 緩やか: StringConstant は通常の文字列値であり、一致は厳密ではないことを意味します。これは、すべての文字列区切り記号を解析された文字列に含める必要がありますが、拡張列は必要な型と部分的に一致する場合があります (必要な型に一致しない拡張列は null 値になります)

* *式*: 文字列に評価される式。

* *ColumnName:*(文字列式から取得された) 値を代入する列の名前を指定します。 
  
* *ColumnType:* 値の変換後の型 (既定では文字列型) を示すスカラー型 (省略可能) を指定する必要があります。

**戻り値**

入力テーブル。演算子に提供される列の一覧に従って拡張されます。

**ヒント**

* [`project`](projectoperator.md)一部の列を削除したり、名前を変更したりする場合は、を使用します。

* 迷惑メールの値をスキップするために、パターンで * を使用します (文字列型の列の後には使用できません)。

* 解析パターンは、*文字列定数*だけでなく、 *ColumnName*で始めることができます。 

* 解析された*式*が文字列型でない場合は、文字列型に変換されます。

* Regex モードが使用されている場合、解析で使用される regex 全体を制御するために regex フラグを追加するオプションがあります。

* Regex モードでは、parse はパターンを正規表現に変換し、 [RE2 構文](re2.md)を使用して、内部で処理される番号付きのキャプチャグループを使用して照合を行います。
  たとえば、次の parse ステートメントは次のようになります。
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    内部解析によって生成される regex は、 `.*?<regex1>(.*?)<regex2>(\-\d+)` です。
        
    - `*`はに変換されました `.*?` 。
        
    - `string`はに変換されました `.*?` 。
        
    - `long`はに変換されました `\-\d+` 。

**使用例**

演算子を使用すると、 `parse` `extend` 同じ式で複数のアプリケーションを使用して、テーブルに効率的にテーブルを提供 `extract` `string` できます。
これは、特に、 `string` 開発者のトレース (" `printf` "/"") ステートメントによって生成された列など、個々の列に分割する複数の値を含む列がテーブルに含まれている場合に便利です `Console.WriteLine` 。

次の例では、テーブルの列にという `EventText` `Traces` 形式の文字列が含まれているとし `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` ます。
次の操作は、、、、、、 `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` 、 `Month` および `Day` の6つの列を含むテーブルを拡張します。 

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
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40: 00.0000000|2016-02-17 08:39: 00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|

regex モードの場合:

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
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00、 |02/17/2016 08:40:00、 |2016-02-17 08:39: 00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01、 |02/17/2016 08:40:01、 |2016-02-17 08:39: 01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01、 |02/17/2016 08:40:01、 |2016-02-17 08:39: 01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00、 |02/17/2016 08:41:00、 |2016-02-17 08:40: 00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01、 |02/17/2016 08:41:00、 |2016-02-17 08:40: 01.0000000|


regex のフラグを使用する regex モードの場合:

ここでは、次のクエリを使用します。

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

ただし、既定のモードは最長一致であるため、期待される結果は得られません。
または、指定された場所に少数のレコードがある場合でも、小文字が大文字になることがあるので、一部の値に対して null が返されることがあります。
期待される結果を得るために、次のように、最短一致でない ( `U` ) と、大文字と小文字を区別する () regex フラグを使用して、このような結果を実行します `i` 。

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


解析された文字列に改行がある場合は、フラグを使用し `s` て、テキストを予期したとおりに解析する必要があります。

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


この例では、緩やかなモードの場合: totalSlices 拡張列は long 型である必要がありますが、解析された文字列では、値が "nonValidLongValue" になっています。
releaseTime 拡張列に同じ問題があります。 nonValidDateTime を datetime として解析することはできません。
この場合、これら2つの拡張列は null 値を取得しますが、他の列 (sliceNumber など) は正しい値を取得します。

以下の同じクエリに対して kind = simple を使用すると、拡張列に対しては厳密であるため、すべての拡張列に対して null が返されます (緩和モードと単純モードでは、拡張列は部分的に一致できます)。

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
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39: 00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39: 01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39: 01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|
