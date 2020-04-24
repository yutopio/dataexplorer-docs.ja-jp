---
title: 解析演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの解析演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: beb51ac80810e5d14013e9b3571d759bb7b09125
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511511"
---
# <a name="parse-operator"></a>parse 演算子

文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 解析に失敗した文字列の場合、計算列には null が含まれます。
解析に失敗した文字列をフィルターで除外する解析[場所](parsewhereoperator.md)演算子を参照してください。

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**構文**

*T* `| parse` `kind=regex` [`flags=regex_flags`]`simple` [ || `*` `*` *Expression* `with` `:` *ColumnType**StringConstant* ] 式 ( 文字列定数*列名*[ 列タイプ ] ) . `relaxed`

**引数**

* *T*: 入力テーブル。
* kind: 

    * 単純 (既定) : StringConstant は通常の文字列値であり、一致は厳密な文字列の文字列の区切り文字は、解析された文字列に表示され、すべての拡張列は、必要な型と一致する必要があります。
        
    * 正規表現 : StringConstant は正規表現である可能性があり、一致は厳密であり、すべての文字列区切り文字 (このモードでは正規表現に設定できます) が解析された文字列に表示され、拡張列はすべて必要な型と一致する必要があります。
    
    * `U` flags : 正規表現モードで使用されるフラグ (Ungreedy)、(`m`複数行モード)、(`s`改行を`\n`一`i`致させる)、(大文字と小文字を区別しない) [RE2 フラグ](re2.md)で.
        
    * relaxed : StringConstant は通常の文字列値であり、すべての文字列区切り記号が解析された文字列に表示される必要がありますが、拡張列は必要な型と部分的に一致する可能性があることを意味します (必要な型と一致しなかった拡張列は値 null を取得します)。

* *式*: 文字列に評価される式。

* *列名:* 文字列式から取り出される値を割り当てる列の名前。 
  
* *ColumnType:* 値を変換する型を示すオプションのスカラー型 (既定では文字列型) にする必要があります。

**戻り値**

入力テーブルは、演算子に提供される列のリストに従って拡張されます。

**ヒント**

* 一[`project`](projectoperator.md)部の列を削除または名前変更する場合に使用します。

* パターンで * を使用して、ジャンク値をスキップします (文字列列の後に使用することはできません)

* 解析パターンは *、文字列定数*のみで始まるだけでなく、*列名*で始まる場合があります。 

* 解析された*式*が string 型でない場合は、型が文字列型に変換されます。

* 正規表現モードを使用する場合は、解析で使用される正規表現全体を制御するために正規表現フラグを追加するオプションがあります。

* 正規表現モードでは、parseはパターンを正規表現に変換し[、RE2構文](re2.md)を使用して、内部的に処理される番号付きキャプチャされたグループを使用してマッチングを行います。
  たとえば、次の解析ステートメントを使用します。
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    内部解析によって生成される正規表現は`.*?<regex1>(.*?)<regex2>(\-\d+)`です。
        
    - `*`に`.*?`翻訳されました。
        
    - `string`に`.*?`翻訳されました。
        
    - `long`に`\-\d+`翻訳されました。

**使用例**

演算子`parse`は、同じ`extend``extract``string`式で複数のアプリケーションを使用して、テーブルへの効率的な方法を提供します。
これは、開発者トレース ("/" `string` `printf`"`Console.WriteLine`ステートメント) ステートメントによって生成された列など、個々の列に分割する複数の値を含む列がテーブルに含まれている場合に最も便利です。

次の例では、テーブル`EventText``Traces`の列に フォーム`Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`の文字列が含まれていると仮定します。
次の操作では、テーブルが 6 列`resourceName`で`totalSlices`拡張`sliceNumber`されます`lockTime `。 `releaseTime` `previouLockTime` `Month` `Day` 


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

|resourceName|合計スライス|スライス番号|ロックタイム|リリースタイム|プレヴィウロックタイム|
|---|---|---|---|---|---|
|パイプラインスケジューラ|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|パイプラインスケジューラ|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|パイプラインスケジューラ|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

正規表現モードの場合:

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

|resourceName|スライス番号|ロックタイム|リリースタイム|プレヴィウロックタイム|
|---|---|---|---|---|
|パイプラインスケジューラ|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|パイプラインスケジューラ|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|パイプラインスケジューラ|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


正規表現フラグを使用して正規表現モードの場合:

リソース名だけを取得する場合は、次のクエリを使用します。

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
|パイプラインスケジューラ、合計スライス=27、スライス数=23、ロックタイム=02/17/2016 08:40:01、リリースタイム=02/17/2016 08:40:01|
|パイプラインスケジューラ、合計スライス=27、スライス番号=15、ロックタイム=02/17/2016 08:40:00、リリースタイム=02/17/2016 08:40:00|
|パイプラインスケジューラ、合計スライス=27、スライス数=20、ロックタイム=02/17/2016 08:40:01、リリースタイム=02/17/2016 08:40:01|
|パイプラインスケジューラ、合計スライス=27、スライス数=22、ロックタイム=02/17/2016 08:41:01、リリース時間=02/17/2016 08:41:00|
|パイプラインスケジューラ、合計スライス=27、スライス番号=16、ロックタイム=02/17/2016 08:41:00、リリースタイム=02/17/2016 08:41:00|

しかし、デフォルトモードは貪欲なので、期待される結果は得られません。
または、resourceName が小文字で表示されるレコードが少ない場合でも、いくつかの値に対して null を取得することがあります。
欲しい結果を得るために、非貪欲な (`U`) を使用してこれを実行し、大文字と小文字を`i`区別する ( ) regex フラグを無効にします。

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
|パイプラインスケジューラ|
|パイプラインスケジューラ|
|パイプラインスケジューラ|
|パイプラインスケジューラ|
|パイプラインスケジューラ|


解析された文字列に改行がある場合は、このフラグ`s`を使用してテキストを解析します。

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

|resourceName|合計スライス|ロックタイム|リリースタイム|前のロックタイム|
|---|---|---|---|---|
|パイプラインスケジューラ<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|パイプラインスケジューラ<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|パイプラインスケジューラ<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


リラックス モードのこの例では、 totalSlice 拡張列は long 型である必要がありますが、解析された文字列では、値 nonValidLongValue が含まれています。
リリースタイム拡張列の問題は同じですが、値 nonValidDateTime を日時として解析することはできません。
この場合、これら 2 つの拡張列は値 null を取得し、他の列 (スライス番号など) は正しい値を取得します。

kind = simple を使用して下の同じクエリに対して、拡張列に厳密であるため、すべての拡張列に対して null が与えられます (リラックス モードとシンプル モードの違いは、リラックス モードでは拡張列を部分的に一致させることができます)。

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

|resourceName|合計スライス|スライス番号|ロックタイム|リリースタイム|プレヴィウロックタイム|
|---|---|---|---|---|---|
|パイプラインスケジューラ|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|パイプラインスケジューラ|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|パイプラインスケジューラ|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|