---
title: シーリング ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのシーリング () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b307121e102229edbe62c4d4dd7f910ea2ce8756
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249330"
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