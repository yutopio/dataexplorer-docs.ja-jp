---
title: シーリング ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのシーリング () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e2a29d28b25d26d582aa49717d5ce5576276f450
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348906"
---
# <a name="ceiling"></a>ceiling()

指定した数値式以上の最小の整数を計算します。

## <a name="syntax"></a>構文

`ceiling(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数。

## <a name="returns"></a>戻り値

* 指定された数値式以上の最小の整数。 

## <a name="examples"></a>例

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|