---
title: getschema オペレーター-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの getschema 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 333c4d59a7ed62fd031ab52019c10abd821fd858
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226756"
---
# <a name="getschema-operator"></a>getschema 演算子 

入力の表形式スキーマを表すテーブルを生成します。

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**構文**

*T* `| ``getschema`

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|[列の型]|
|---|---|---|---|
|Timestamp|0|System.DateTime|datetime|
|Language|1|System.String|string|
|ページ|2|System.String|string|
|ビュー|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
