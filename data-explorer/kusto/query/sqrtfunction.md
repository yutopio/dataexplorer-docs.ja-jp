---
title: sqrt ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの sqrt () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 76f5c8c5f8c1a0b9f685ae88df1ab624dc446150
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350963"
---
# <a name="sqrt"></a>sqrt()

平方根関数を返します。  

## <a name="syntax"></a>構文

`sqrt(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数 >= 0 です。

## <a name="returns"></a>戻り値

* `sqrt(x) * sqrt(x) == x`
* 引数が負であるか、`real` 値に変換できない場合は `null`。 