---
title: toguid ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの toguid () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 689ee6bf7b3fcb27dced20b06a9002659622902e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804136"
---
# <a name="toguid"></a>toguid()

入力を表現に変換 [`guid`](./scalar-data-types/guid.md) します。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> 可能な場合は、 [guid ()](./scalar-data-types/guid.md)を使用することをお勧めします。

## <a name="syntax"></a>構文

`toguid(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: スカラーに変換される式 [`guid`](./scalar-data-types/guid.md) 。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は [`guid`](./scalar-data-types/guid.md) スカラーになります。
変換に失敗した場合、結果はになり `null` ます。
