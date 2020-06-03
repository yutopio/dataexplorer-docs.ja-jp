---
title: . alter インジェスト mapping-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの alter インジェストマッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f62692c7f5a1b557038f452f5ed3c023ec9849f9
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329029"
---
# <a name="alter-ingestion-mapping"></a>.alter インジェスト マッピング

特定のテーブルおよび特定の形式 (完全マッピングの置換) に関連付けられている既存のインジェストマッピングを変更します。

**構文**

`.alter``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * このマッピングは、コマンドの一部として完全なマッピングを指定するのではなく、インジェストコマンドを使用して名前で参照できます。
> * _Mappingkind_の有効な値は `CSV` 、、、 `JSON` `avro` 、 `parquet` 、および `orc` です。

**例** 
 
```kusto
.alter table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"} }'
']'

.alter table MyTable ingestion json mapping "Mapping1"
'['
'   { "column" : "rownumber", "Properties":{"Path":"$.rownumber"}},'
'   { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**サンプル出力**

| 名前     | 種類 | マッピング                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
