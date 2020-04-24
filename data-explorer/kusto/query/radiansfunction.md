---
title: ラジアン() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの radians() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0dc04c5f9593b6bd5fc61f57d20819cf7d2a178c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510661"
---
# <a name="radians"></a>radians()

数式を使用して、度の角度値をラジアン単位の値に変換します。`radians = (PI / 180 ) * angle_in_degrees`

**構文**

`radians(`*A*`)`

**引数**

* *a*: 角度 (実数)

**戻り値**

* 角度で指定された角度のラジアン単位の対応する角度。 

**使用例**

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|ラジアン0|ラジアン1|ラジアン2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|