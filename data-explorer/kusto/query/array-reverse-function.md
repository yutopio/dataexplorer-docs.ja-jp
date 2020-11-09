---
title: array_reverse ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_reverse () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 327564a953c8f537a0f76727b4313749f61eec3e
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94374012"
---
# <a name="array_reverse"></a>array_reverse ()

動的配列内の要素の順序を反転させます。

## <a name="syntax"></a>構文

`array_reverse(`*array*`)`

## <a name="arguments"></a>引数

*array* : 反転する入力配列。

## <a name="returns"></a>戻り値

入力配列とまったく同じ要素が逆順で格納されている配列。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|結果|
|---|
|["例"、"an"、"is"、"this"]|
