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
ms.openlocfilehash: 4fe19148ef8f410f04dc68f435734a2c2c425cca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347682"
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
|ビュー|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
