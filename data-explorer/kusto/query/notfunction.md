---
title: not ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにおける not () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3224c48b963c051d0d65d27a2e64ec8317be30c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252188"
---
# <a name="not"></a>not()

引数の値を反転させ `bool` ます。

```kusto
not(false) == true
```

## <a name="syntax"></a>構文

`not(`*with*`)`

## <a name="arguments"></a>引数

* *expr*: `bool` 逆順にする式。

## <a name="returns"></a>戻り値

引数の反転された論理値を返し `bool` ます。