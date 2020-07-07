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
ms.openlocfilehash: 4a225c7d9cb1c5f99434c4595cbd798d020cfd9f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967436"
---
# <a name="show-ingestion-mapping"></a>.show インジェスト マッピング

インジェストマッピング (すべてまたは名前で指定されたもの) を表示します。

* `.show``table` *TableName* `ingestion` *mappingkind*  `mappings`

* `.show``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

すべてのマッピングの種類からのすべてのインジェストマッピングを表示する:

* `.show` `table` *TableName* `ingestion`  `mapping`
 
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
