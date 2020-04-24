---
title: format_bytes() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで format_bytes() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5d5bfa234e001f498737b9df88274096f8592f52
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515166"
---
# <a name="format_bytes"></a>format_bytes()

数値をデータ サイズ (バイト単位) を表す文字列として書式設定します。

```kusto
format_bytes(1024) == '1 KB'"
```

**構文**

`format_bytes(`*値*`,` *precision* [`,`精度 [*単位*] ]`)`

**引数**

* `value`: データ サイズ (バイト単位) として書式設定する数値。
* `precision`: (オプション) 値が丸められる桁数。 (デフォルト値は 0 です)。
* `units`: (オプション) 文字列の書式設定で使用するターゲット データ`Bytes`サイズ`KB`の`MB`単位`GB` `TB`( `PB`, , , , ) 。 パラメータが空の場合、単位は入力値に基づいて自動選択されます。

**戻り値**

書式の結果を含む文字列。

**使用例**

```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|v2|v3|v4|v5|
|---|---|---|---|---|
|564 バイト|10.1 KB|19 MB|19.08 MB|19541 KB|