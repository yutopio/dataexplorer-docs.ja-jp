---
title: parse 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラー内の解析対象演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: c0ee38fe77c0957b9ba7fd589115eee20be6a649
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224852"
---
# <a name="parse-where-operator"></a>parse-where 演算子

文字列式を評価し、その値を1つ以上の計算列に解析します。 結果は、正常に解析された文字列のみになります。
解析できなかった文字列に対して null を生成する[parse operator](parseoperator.md)を参照してください。

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**構文**

*T* `| parse-where` [ `kind=regex` [ `flags=regex_flags` ] | `simple` ]*式* `with` `*` (*stringconstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**引数**

* *T*: 入力テーブル。

* *種類*: 

    * *simple* (既定値): stringconstant は通常の文字列値で、一致は strict です。 すべての文字列の区切り記号が解析された文字列に含まれ、すべての拡張列が必要な型と一致している必要があります。
        
    * *regex*: stringconstant は正規表現にすることができ、一致は厳格です。 すべての文字列の区切り記号が解析された文字列に含まれ、すべての拡張列が必要な型と一致している必要があります。 このモードでは、文字列の区切り記号を正規表現にすることができます。
    
    * *flags*: regex モードで使用されるフラグ: `U` (非最長一致)、 `m` (複数行モード)、( `s` 新しい行に一致)、( `\n` `i` 大文字と小文字は区別されません)。 [RE2 flags](re2.md)には、より多くのフラグがあります。
        
* *式*: 文字列に評価される式。

* *ColumnName:* 文字列式から取得された値に割り当てられている列の名前。 
  
* *ColumnType:* 値の変換後の型を示す、省略可能なスカラー型を指定する必要があります。 既定値は string 型です。

**戻り値**

入力テーブル。演算子に提供される列の一覧に従って拡張されます。

> [!Note] 
> 出力には、正常に解析された文字列のみが含まれます。 パターンに一致しない文字列は除外されます。

**ヒント**

* `parse-where`は、[解析](parseoperator.md)と同じ方法で文字列を解析し、正常に解析されなかった文字列を除外します。

* 一部の列を削除または名前変更する場合は、 [project](projectoperator.md)を使用します。

* 迷惑メールの値をスキップするには、パターンで * を使用します。 文字列型の列の後にこの値を使用することはできません。

* Parse パターンは、*文字列定数*に加えて、 *ColumnName*で始まる場合があります。 

* 解析された*式*が文字列型でない場合は、文字列型に変換されます。

* Regex モードが使用されている場合、regex フラグを追加して、解析で使用される regex 全体を制御できます。

* Regex モードでは、解析はパターンを正規表現に変換し、 [RE2 構文](re2.md)を使用して、内部で処理される番号付きのキャプチャグループを使用して照合を行います。
  
  たとえば、次の parse ステートメントを使用します。
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    内部解析によって生成される regex は、 `.*?<regex1>(.*?)<regex2>(\-\d+)` です。
        
    - `*`はに変換されました `.*?` 。
        
    - `string`はに変換されました `.*?` 。
        
    - `long`はに変換されました `\-\d+` 。

## <a name="examples"></a>使用例

演算子を使用すると、 `parse-where` `extend` 同じ式で複数のアプリケーションを使用して、テーブルに効率的にテーブルを提供 `extract` `string` できます。 これは、テーブルに、 `string` 個々の列に分割する複数の値を含む列がある場合に最も役立ちます。 たとえば、開発者のトレース (" `printf` "/"") ステートメントによって生成された列を分割でき `Console.WriteLine` ます。

### <a name="using-parse"></a>`parse` の使用

次の例では、テーブルの列にという `EventText` `Traces` 形式の文字列が含まれてい `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` ます。 次の操作は、、、、、、、、およびの6つの列を含むテーブルを拡張します `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` `Month` `Day` 。 

一部の文字列には完全一致がありません。

を使用すると、 `parse` 計算列には null が設定されます。

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
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|前の Locktime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|

### <a name="using-parse-where"></a>`parse-where` の使用 

' Parse ' を使用すると、結果から解析された文字列をフィルターで除外できます。

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
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|前の Locktime|
|---|---|---|---|---|---|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40: 01.0000000|2016-02-17 08:39: 01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41: 00.0000000|2016-02-17 08:40: 01.0000000|


### <a name="regex-mode-using-regex-flags"></a>Regex フラグを使用した regex モード

次のクエリを使用して、に対して、/を取得します。

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

### <a name="parse-where-with-case-insensitive-regex-flag"></a>`parse-where`大文字と小文字を区別しない regex フラグ

上記のクエリでは、既定のモードでは大文字と小文字が区別されているため、文字列は正常に解析されました。 結果は取得されませんでした。

必要な結果を得るには、 `parse-where` 大文字と小文字を区別しない () regex フラグを指定してを実行し `i` ます。

3つの文字列のみが正常に解析されるため、結果は3つのレコードになります (一部の totalSlices は無効な整数を保持します)。

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

|resourceName|totalSlices|
|---|---|
|PipelineScheduler|27|
|PipelineScheduler|27|
|PipelineScheduler|27|
