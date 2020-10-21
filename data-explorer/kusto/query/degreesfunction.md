---
title: degrees ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの degrees () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04efbee59bce153de76ab5d44617b8d1bebc121c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253091"
---
# <a name="degrees"></a>degrees()

式を使用して、ラジアン単位の角度の値を度数で値に変換します。 `degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>構文

`degrees(`*ある*`)`

## <a name="arguments"></a>引数

* *a*: ラジアン (実数) の角度。

## <a name="returns"></a>戻り値

* ラジアン単位で指定された角度に対応する角度 (度単位)。 

## <a name="examples"></a>例

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
