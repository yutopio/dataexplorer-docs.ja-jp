---
title: 。インジェストマッピングの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのインジェストマッピングの表示について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3c19426410046d7ff2357b4967333db8b039d9e6
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328995"
---
# <a name="show-ingestion-mappings"></a>。インジェストマッピングを表示します。

インジェストマッピング (すべてまたは名前で指定されたもの) を表示します。

* `.show``table` *TableName* `ingestion` *mappingkind*  `mappings`

* `.show``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

すべてのマッピングの種類からのすべてのインジェストマッピングを表示する:

* `.show``table` *TableName*`ingestion`  `mapping`
 
**例** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**サンプル出力**

| 名前     | 種類 | マッピング     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
