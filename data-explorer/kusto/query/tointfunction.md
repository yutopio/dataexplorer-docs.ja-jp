---
title: toint ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの toint () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2daea4d190ed349c41a8eecf2eef53b2c2b93716
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350691"
---
# <a name="toint"></a>toint()

入力を整数 (符号付き32ビット) の数値表現に変換します。

```kusto
toint("123") == int(123)
```

## <a name="syntax"></a>構文

`toint(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: 整数に変換される式。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は整数になります。
変換が成功しなかった場合、結果はになり `null` ます。
 
*注*: 可能な限り[int ()](./scalar-data-types/int.md)を使用することをお勧めします。
