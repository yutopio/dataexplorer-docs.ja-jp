---
title: ラジアン ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのラジアン () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bacb005b8828c63efac1737c527cc313a3ee052b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241328"
---
# <a name="radians"></a>radians()

式を使用して、角度 (度単位) の値をラジアンで値に変換します。 `radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>構文

`radians(`*ある*`)`

## <a name="arguments"></a>引数

* *a*: 角度 (実数)。

## <a name="returns"></a>戻り値

* ラジアン単位で指定された角度に対応する角度 (度単位)。 

## <a name="examples"></a>例

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979)|6.28318530717959|