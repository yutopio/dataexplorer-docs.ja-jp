---
title: tostring ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの tostring () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2093ff1117cf7744af550cf93c3fe630fa40a6e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340177"
---
# <a name="tostring"></a>tostring()

入力を文字列形式に変換します。

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>構文

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>引数

* *`Expr`*: 文字列に変換される式。 

## <a name="returns"></a>戻り値

*`Expr`* 値が null 以外の場合、結果はの文字列表現になり *`Expr`* ます。
*`Expr`* 値が null の場合、結果は空の文字列になります。
 