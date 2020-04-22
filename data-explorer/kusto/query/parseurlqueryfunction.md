---
title: parse_urlquery() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで parse_urlquery() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f00fefcd6245528d7ae50d6046d97289a92317d
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744621"
---
# <a name="parse_urlquery"></a>parse_urlquery()

オブジェクトに`dynamic`クエリ パラメーターが含まれている場合に返します。

**構文**

`parse_urlquery(`*クエリ*`)`

**引数**

* *query*: 文字列は URL クエリを表します。

**戻り値**

クエリ パラメーターを含む[動的](./scalar-data-types/dynamic.md)型のオブジェクト。

**例**

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

結果になります:

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**メモ**

* 入力形式は、URL クエリ標準に従う必要があります (キー = 値& .
 