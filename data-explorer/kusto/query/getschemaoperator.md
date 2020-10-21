---
title: getschema オペレーター-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの getschema 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6960a737b0a71a6b921a6a58750e48f5c3fb9da0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247382"
---
# <a name="getschema-operator"></a>getschema 演算子 

入力の表形式スキーマを表すテーブルを生成します。

```kusto
T | summarize MyCount=count() by Country | getschema 
```

## <a name="syntax"></a>構文

*T* `| ``getschema`

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|[列の型]|
|---|---|---|---|
|Timestamp|0|System.DateTime|DATETIME|
|Language|1|System.String|string|
|ページ|2|System.String|string|
|Views|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
