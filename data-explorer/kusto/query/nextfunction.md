---
title: 次へ ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの次の () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ca9361c0a43a2881f7448312e4f8a5129426e55a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248726"
---
# <a name="next"></a>next()

シリアル化された [行セット](./windowsfunctions.md#serialized-row-set)の現在の行の後にあるオフセット位置にある行の列の値を返します。

## <a name="syntax"></a>構文

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>引数

* `column`: 値の取得元の列。

* `offset`: 行の前に来るオフセット。 オフセットが指定されていない場合、既定のオフセット1が使用されます。

* `default_value`: 値を取得する次の行がない場合に使用される既定値。 既定値が指定されていない場合、null が使用されます。


## <a name="examples"></a>使用例
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```