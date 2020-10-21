---
title: parse_url ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_url () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ce68151270369bd7dc0898b98029bacddf0c243
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248632"
---
# <a name="parse_url"></a>parse_url()

絶対 URL を解析 `string` し、 `dynamic` url 部分を格納しているオブジェクトを返します。


## <a name="syntax"></a>構文

`parse_url(`*先*`)`

## <a name="arguments"></a>引数

* *url*: url または url のクエリ部分を表す文字列。

## <a name="returns"></a>戻り値

URL コンポーネント (Scheme、Host、Port、Path、Username、Password、Query Parameters、Fragment) を含む、 [dynamic](./scalar-data-types/dynamic.md) 型のオブジェクト。

## <a name="example"></a>例

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

結果は

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