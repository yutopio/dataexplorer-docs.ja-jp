---
title: estimate_data_size ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの estimate_data_size () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09d722b07b3ff49a294d4d8ce19a89563fc8f66e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253025"
---
# <a name="estimate_data_size"></a>estimate_data_size()

表形式の式で選択されている列の推定データサイズ (バイト単位) を返します。

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

## <a name="syntax"></a>構文

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, `*col2* `, `...`)`

## <a name="arguments"></a>引数

* *col1*, *col2*: データサイズの推定に使用されるソース表形式式の列参照の選択。 すべての列を含めるには、 `*` (アスタリスク) 構文を使用します。

## <a name="returns"></a>戻り値

* レコードサイズの推定データサイズ (バイト単位)。 推定は、データ型と値の長さに基づいています。

## <a name="examples"></a>例

合計データサイズの計算に使用する `estimated_data_size()` もの:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|合計|
|---|
|180|
