---
title: estimate_data_size() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで estimate_data_size() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9664a7c9caf0506cdb7274eed5e143f89c82cebb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515727"
---
# <a name="estimate_data_size"></a>estimate_data_size()

表形式の式で選択された列の推定データ サイズをバイト単位で返します。

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**構文**

`estimate_data_size(*)`

`estimate_data_size(`*コル1*`, `*コル2..*`, ``)`

**引数**

* *col1* *,col2*: データサイズの推定に使用されるソース表形式の式の列参照の選択。 すべての列を含めるには`*`、(アスタリスク)構文を使用します。

**戻り値**

* レコード サイズの推定データ サイズ (バイト単位)。 推定は、データ型と値の長さに基づいています。

**使用例**

を使用して合計データ`estimated_data_size()`サイズを計算する:

```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|合計|
|---|
|180|