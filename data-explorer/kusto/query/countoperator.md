---
title: カウント演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの count 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 14895e17d77e3bbf1d98d2d68221930d3f291774
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644622"
---
# <a name="count-operator"></a>count 演算子

入力レコードセット内のレコードの数を返します。

## <a name="syntax"></a>構文

`T | count`

## <a name="arguments"></a>引数

*T*: カウントするレコードが含まれる表形式のデータ。

## <a name="returns"></a>戻り値

この関数は、1 つのレコードと `long`型の列を含むテーブルを返します。 唯一のセルの値は、 *T* 内のレコードの数です。 

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>関連項目

Count () 集計関数の詳細については、「 [count () (集計関数)](count-aggfunction.md)」を参照してください。
