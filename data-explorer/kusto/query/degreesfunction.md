---
title: degrees ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの degrees () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41d679ea1add3706de5012f4e4fbf382e1f7b3ee
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348379"
---
# <a name="degrees"></a>degrees()

式を使用して、ラジアン単位の角度の値を度数で値に変換します。`degrees = (180 / PI ) * angle_in_radians`

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
