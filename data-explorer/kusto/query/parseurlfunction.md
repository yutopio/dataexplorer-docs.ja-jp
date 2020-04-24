---
title: parse_url() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでparse_url() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfc093964ce5b91acc01f798f8f62651266ab153
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511494"
---
# <a name="parse_url"></a>parse_url()

絶対 URL`string`を解析し、URL 部分を含むオブジェクトを`dynamic`返します。


**構文**

`parse_url(`*Url*`)`

**引数**

* *url*: URL または URL のクエリ部分を表す文字列。

**戻り値**

URL コンポーネントを含む[動的](./scalar-data-types/dynamic.md)な型のオブジェクト: スキーム、ホスト、ポート、パス、ユーザー名、パスワード、クエリ パラメーター、フラグメント。

**例**

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

結果になります

```
 {
    "Scheme":"scheme",
    "Host":"host",
    "Port":"1234",
    "Path":"this/is/a/path",
    "Username":"username",
    "Password":"password",
    "Query Parameters":"{"k1":"v1", "k2":"v2"}",
    "Fragment":"fragment"
 }
```