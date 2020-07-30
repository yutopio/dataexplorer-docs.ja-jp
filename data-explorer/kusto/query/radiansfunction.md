---
title: ラジアン ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのラジアン () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3aaa41a631498e2938acf722b75f409a1bbe5031
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345948"
---
# <a name="radians"></a>radians()

式を使用して、角度 (度単位) の値をラジアンで値に変換します。`radians = (PI / 180 ) * angle_in_degrees`

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