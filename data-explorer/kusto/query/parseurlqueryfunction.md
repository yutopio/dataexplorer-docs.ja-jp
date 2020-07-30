---
title: parse_urlquery ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_urlquery () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d34ece3a945485b8a809089d030fa954b070a28
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346271"
---
# <a name="parse_urlquery"></a>parse_urlquery()

`dynamic`クエリパラメーターを格納しているオブジェクトを返します。

## <a name="syntax"></a>構文

`parse_urlquery(`*照会*`)`

## <a name="arguments"></a>引数

* *query*: 文字列は url クエリを表します。

## <a name="returns"></a>戻り値

クエリパラメーターを含む、 [dynamic](./scalar-data-types/dynamic.md)型のオブジェクト。

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

**メモ**

* 入力形式は URL クエリ標準に従う必要があります (キー = 値&...)
 