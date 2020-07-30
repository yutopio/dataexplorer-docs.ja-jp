---
title: totimespan ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの totimespan () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1edc5e3ef8c3c2dea65d332e6ceace653cc5c812
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340141"
---
# <a name="totimespan"></a>totimespan()

入力を[timespan](./scalar-data-types/timespan.md)スカラーに変換します。

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>構文

`totimespan(Expr)`

## <a name="arguments"></a>引数

* *`Expr`*: [Timespan](./scalar-data-types/timespan.md)に変換される式。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は[timespan](./scalar-data-types/timespan.md)値になります。
それ以外の場合、結果は null になります。
