---
title: ストレップ() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで strrep() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 39b398e8fadb400c25cfeb97487c2ecf0669ad83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506717"
---
# <a name="strrep"></a>strrep()

指定された[文字列](./scalar-data-types/string.md)の繰り返し回数。

* 最初または 3 番目の引数が文字列型でない場合は、文字列に強制的に変換されます。

**構文**

`strrep(`*値*、*乗数*、区切り記号 [区切*り記号*]`)`

**引数**

* *値*: 入力式
* *乗数*: 正の整数値 (1 ~ 1024)
* *区切り文字*: オプションの文字列式 (デフォルト: 空の文字列)

**戻り値**

指定した回数繰り返される値を*区切り文字*と連結します。

乗*数*が最大許容値 (1024) を超える場合、入力文字列は 1024 回繰り返されます。
 
**例**

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|