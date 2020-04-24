---
title: trim_start() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの trim_start() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d4ae71f73e76005f89766d974192c8eb24cd74d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505578"
---
# <a name="trim_start"></a>trim_start()

指定した正規表現の先頭に一致する文字列を削除します。

**構文**

`trim_start(`*正規表現*`,`*テキスト*`)`

**引数**

* *正規表現*:*テキスト*の先頭からトリムする文字列または[正規表現](re2.md)。  
* *text*: 文字列。

**戻り値**

*テキスト*の先頭にある*正規表現*の一致をトリミングした後の*テキスト*。

**例**

ステートメントベローズは *、string_to_trim*の先頭から*部分文字列*をトリミングします。

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

Next ステートメントは、文字列の先頭からすべての単語以外の文字をトリムします。

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- テ st1// $|テ st1// $|
|- テ st2// $|テ st2// $|
|- テ st3// $|テ st3// $|
|- テ st4// $|テ st4// $|
|- テ st5// $|テ st5// $|

 