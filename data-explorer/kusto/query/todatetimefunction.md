---
title: todatetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの todatetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a991f9f64decbb3985830cf2611361d6c66db31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350844"
---
# <a name="todatetime"></a>todatetime()

入力を[datetime](./scalar-data-types/datetime.md)スカラーに変換します。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>構文

`todatetime(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: [datetime](./scalar-data-types/datetime.md)に変換される式。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は[datetime](./scalar-data-types/datetime.md)値になります。
それ以外の場合、結果は null になります。
 
> [!NOTE]
> 可能な場合は、 [datetime ()](./scalar-data-types/datetime.md)を使用することをお勧めします。
