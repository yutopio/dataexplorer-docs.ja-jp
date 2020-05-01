---
title: . alter インジェスト mapping-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの alter インジェストマッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f43039ff3935edbb6e92627d2f96b1c411e1ffa
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617818"
---
# <a name="alter-ingestion-mapping"></a>.alter インジェスト マッピング

特定のテーブルおよび特定の形式 (完全マッピングの置換) に関連付けられている既存のインジェストマッピングを変更します。

**構文**

`.alter``table` *TableName* TableName `ingestion` *MappingKind* mappingkind `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * このマッピングは、コマンドの一部として完全なマッピングを指定するのではなく、インジェストコマンドを使用して名前で参照できます。
> * _Mappingkind_の有効な値は`CSV`、 `JSON`、 `avro`、 `parquet`、、 `orc`およびです。

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

**出力例**

| 名前     | 種類 | マッピング                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{"Name": "rownumber"、"DataType": "int"、"CsvDataType": null、"Ordinal": 0、"": null}、{"Name": "rowguid"、"DataType": "string"、"CsvDataType": null、"Ordinal": 1、"": null}] |
