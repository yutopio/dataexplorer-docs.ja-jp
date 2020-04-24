---
title: 解析先オペレータ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの解析先演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 0e44ab83242fc8aed0e46bdab1fa5a142992bfe5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511409"
---
# <a name="parse-where-operator"></a>parse-where 演算子

文字列式が評価され、その値が 1 つまたは複数の計算列に解析されます。 結果は、正常に解析された文字列のみです。
解析に失敗した文字列については、null を生成する[解析演算子を](parseoperator.md)参照してください。

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**構文**

*T* `| parse-where` `kind=regex` [`flags=regex_flags`]`simple`[ ] |*Expression*`with`式`*`(*文字列定数**列名*[`:` `*`*列タイプ*] ) .

**引数**

* *T*: 入力テーブル。

* kind: 

    * 単純 (既定) : StringConstant は通常の文字列値であり、一致は厳密な文字列の文字列の区切り文字は、解析された文字列に表示され、すべての拡張列は、必要な型と一致する必要があります。
        
    * 正規表現 : StringConstant は正規表現である可能性があり、一致は厳密であり、すべての文字列区切り文字 (このモードでは正規表現に設定できます) が解析された文字列に表示され、拡張列はすべて必要な型と一致する必要があります。
    
    * `U` flags : 正規表現モードで使用されるフラグ (Ungreedy)、(`m`複数行モード)、(`s`改行を`\n`一`i`致させる)、(大文字と小文字を区別しない) [RE2 フラグ](re2.md)で.
        
* *式*: 文字列に評価される式。

* *列名:* 文字列式から取り出される値を割り当てる列の名前。 
  
* *ColumnType:* 値を変換する型を示すオプションのスカラー型 (既定では文字列型) にする必要があります。

**戻り値**

入力テーブルは、演算子に提供される列のリストに従って拡張されます。
正常に解析された文字列のみが出力に含まれます。 パターンに一致しない文字列は、フィルターで除外されます。

**ヒント**

* `parse-where`解析するのと同じ方法で文字列を[解析しますが](parseoperator.md)、正常に解析されなかった文字列もフィルタアウトします。

* 一[`project`](projectoperator.md)部の列を削除または名前変更する場合に使用します。

* パターンで * を使用して、ジャンク値をスキップします (文字列列の後に使用することはできません)

* 解析パターンは *、文字列定数*のみで始まるだけでなく、*列名*で始まる場合があります。 

* 解析された*式*が string 型でない場合は、型が文字列型に変換されます。

* 正規表現モードを使用する場合は、解析で使用される正規表現全体を制御するために正規表現フラグを追加するオプションがあります。

* 正規表現モードでは、parseはパターンを正規表現に変換し[、RE2構文](re2.md)を使用して、内部的に処理される番号付きキャプチャされたグループを使用してマッチングを行います。
  
  たとえば、次の解析ステートメントを使用します。
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    内部解析によって生成される正規表現は`.*?<regex1>(.*?)<regex2>(\-\d+)`です。
        
    - `*`に`.*?`翻訳されました。
        
    - `string`に`.*?`翻訳されました。
        
    - `long`に`\-\d+`翻訳されました。

**使用例**

演算子`parse-where`は、同じ`extend``extract``string`式で複数のアプリケーションを使用して、テーブルへの効率的な方法を提供します。
これは、開発者トレース ("/" `string` `printf`"`Console.WriteLine`ステートメント) ステートメントによって生成された列など、個々の列に分割する複数の値を含む列がテーブルに含まれている場合に最も便利です。

次の例では、テーブル`EventText``Traces`の列に フォーム`Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`の文字列が含まれていると仮定します。
次の操作では、テーブルが 6 列`resourceName`で`totalSlices`拡張`sliceNumber`されます`lockTime `。 `releaseTime` `previouLockTime` `Month` `Day` 

ここでは、完全に一致しない文字列はほとんどありません。
を`parse`使用すると、計算列には NULL 値が設定されます。

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|合計スライス|スライス番号|ロックタイム|リリースタイム|プレヴィウロックタイム|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|パイプラインスケジューラ|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


を`parse-where`使用すると、結果から解析されなかった文字列をフィルターで除外します。

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|合計スライス|スライス番号|ロックタイム|リリースタイム|プレヴィウロックタイム|
|---|---|---|---|---|---|
|パイプラインスケジューラ|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|パイプラインスケジューラ|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


正規表現フラグを使用して正規表現モードの場合:

もし我々がresouceNameとtotalSlicesを取得することに興味があり、このクエリを使用するならば:

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

上記のクエリでは、既定のモードでは大文字と小文字が区別されるため、どの文字列も正常に解析されなかったため、結果は得られない。

必要な結果を得るために、大文字と小文字を`parse-where`区別しない (`i`) regex フラグを使用して実行します。
3 つの文字列だけが正常に解析されるため、結果は 3 レコードになります (一部の totalSlice には無効な整数が保持されます)。 

```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex flags=i EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

|resourceName|合計スライス|
|---|---|
|パイプラインスケジューラ|27|
|パイプラインスケジューラ|27|
|パイプラインスケジューラ|27|


