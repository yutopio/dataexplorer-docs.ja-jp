---
title: toguid ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの toguid () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d122833f4797c8503dd41cc8ba861554d6924338
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250387"
---
# <a name="toguid"></a>toguid()

入力を表現に変換 [`guid`](./scalar-data-types/guid.md) します。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> 可能な場合は、 [guid ()](./scalar-data-types/guid.md) を使用することをお勧めします。

## <a name="syntax"></a>構文

`toguid(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: スカラーに変換される式 [`guid`](./scalar-data-types/guid.md) 。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は [`guid`](./scalar-data-types/guid.md) スカラーになります。
変換に失敗した場合、結果はになり `null` ます。
