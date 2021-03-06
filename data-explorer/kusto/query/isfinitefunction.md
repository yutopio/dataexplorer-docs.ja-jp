---
title: isfinite ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isfinite () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b428d43afd7984bbcf19351da702517a3a1244a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242312"
---
# <a name="isfinite"></a>isfinite()

入力が有限値である (無限でも NaN でもない) かどうかを返します。

## <a name="syntax"></a>構文

`isfinite(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数。

## <a name="returns"></a>戻り値

X が有限の場合は0以外の値 (true)。それ以外の場合は 0 (false) です。

## <a name="see-also"></a>関連項目

* 値が null かどうかを確認する方法については、「 [isnull ()](isnullfunction.md)」を参照してください。
* 値が無制限かどうかを確認する方法については、「 [isinf ()](isinffunction.md)」を参照してください。
* 値が NaN (非数) であるかどうかを確認する方法については、「 [isnan ()](isnanfunction.md)」を参照してください。

## <a name="example"></a>例

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isfinite|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|