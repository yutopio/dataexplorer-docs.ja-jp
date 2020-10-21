---
title: parse_urlquery ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_urlquery () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1092edfaca8c6789ec6c0dc478fb27505531b1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244697"
---
# <a name="parse_urlquery"></a>parse_urlquery()

`dynamic`クエリパラメーターを格納しているオブジェクトを返します。

## <a name="syntax"></a>構文

`parse_urlquery(`*照会*`)`

## <a name="arguments"></a>引数

* *query*: 文字列は url クエリを表します。

## <a name="returns"></a>戻り値

クエリパラメーターを含む、 [dynamic](./scalar-data-types/dynamic.md) 型のオブジェクト。

## <a name="example"></a>例

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

結果は次のようになります。

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**ノート**

* 入力形式は URL クエリ標準に従う必要があります (キー = 値&...)
 