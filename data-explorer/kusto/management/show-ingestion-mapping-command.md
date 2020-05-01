---
title: 。インジェストマッピングを表示します-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのインジェストマッピングの表示について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 711861a07896b7bdc4cf57bebbf1cdd0e01d064a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617172"
---
# <a name="show-ingestion-mappings"></a>。インジェストマッピングを表示します。

インジェストマッピング (すべてまたは名前で指定されたもの) を表示します。

* `.show``table` *TableName* TableName `ingestion` *mappingkind*  `mappings`

* `.show``table` *TableName* TableName `ingestion` *MappingKind* mappingkind`mapping` *MappingName*   

すべてのマッピングの種類からすべてのインジェストマッピングを表示する:

* `.show` `table` *TableName* `ingestion`  `mapping`
 
**例** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**出力例**

| 名前     | 種類 | マッピング     |
|----------|------|-------------|
| mapping1 | CSV  | [{"Name": "rownumber"、"DataType": "int"、"CsvDataType": null、"Ordinal": 0、"": null}、{"Name": "rowguid"、"DataType": "string"、"CsvDataType": null、"Ordinal": 1、"": null}] |
