---
title: trim_end() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでtrim_end() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a6f6ffc264cb436fc61d74f08dfded915caa05d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505646"
---
# <a name="trim_end"></a>trim_end()

指定した正規表現の末尾一致を削除します。

**構文**

`trim_end(`*正規表現*`,`*テキスト*`)`

**引数**

* *正規表現*:*テキスト*の末尾からトリミングする文字列または[正規表現](re2.md)。  
* *text*: 文字列。

**戻り値**

*テキスト*の最後に見つかった*正規表現*の一致をトリミングした後の*テキスト*。

**例**

ステートメントベローズは *、string_to_trim*の終わりから*部分文字列*をトリミングします。

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing.          |

Next ステートメントは、文字列の末尾からすべての単語以外の文字をトリムします。

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|- テ st1// $|- テ st1  |
|- テ st2// $|- テ st2  |
|- テ st3// $|- テ st3  |
|- テ st4// $|- テ st4  |
|- テ st5// $|- テ st5  |