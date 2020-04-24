---
title: 度() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの degrees() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1365d6c6629f4f4769d7c4b62491eaec25e4ec59
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516033"
---
# <a name="degrees"></a>degrees()

式を使用して、ラジアンの角度値を度単位の値に変換します。`degrees = (180 / PI ) * angle_in_radians`

**構文**

`degrees(`*A*`)`

**引数**

* *a*: ラジアン単位の角度 (実数)。

**戻り値**

* ラジアン単位で指定された角度に対応する角度 (度単位)。 

**使用例**

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|度0|度1|度2|
|---|---|---|
|45|270|0|
