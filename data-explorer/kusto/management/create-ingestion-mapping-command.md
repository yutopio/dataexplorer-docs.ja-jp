---
title: ': インジェストマッピングを作成する-Azure データエクスプローラー'
description: この記事では、Azure データエクスプローラーでインジェストマッピングを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3855d56ad31bbf98a6a075feb44a598b3bdbf52a
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329063"
---
# <a name="create-ingestion-mapping"></a>.create インジェスト マッピング

特定のテーブルおよび特定の形式に関連付けられているインジェストマッピングを作成します。

**構文**

`.create``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * 作成されたマッピングは、コマンドの一部として完全なマッピングを指定するのではなく、インジェストコマンドで名前によって参照できます。
> * _Mappingkind_の有効な値は `CSV` 、、、 `JSON` `avro` 、 `parquet` 、およびです。`orc`
> * 同じ名前のマッピングがテーブルに既に存在する場合は、次のようになります。
>    * `.create`失敗します
>    * `.create-or-alter`既存のマッピングを変更します
 
**例** 
 
```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create-or-alter table MyTable ingestion json mapping "Mapping1"
'['
'    { "column" : "rownumber", "datatype" : "int", "Properties":{"Path":"$.rownumber"}},'
'    { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**出力例**

| 名前     | 種類 | マッピング                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |

## <a name="next-steps"></a>次のステップ
インジェストマッピングの詳細については、「[データマッピング](mappings.md)」を参照してください。
