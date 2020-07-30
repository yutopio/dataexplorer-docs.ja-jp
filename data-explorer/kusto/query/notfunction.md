---
title: not ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにおける not () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fed2a55c8fa1c7689c087ccdeaa64ff576bea401
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346594"
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