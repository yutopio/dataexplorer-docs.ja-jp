---
title: .alter インジェスション マッピング - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .alter インジェスティマッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5343d55fadafce552c5d837e5eb50763ccf45a4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522408"
---
# <a name="alter-ingestion-mapping"></a>.alter インジェスト マッピング

特定のテーブルと特定の形式 (完全マッピング置換) に関連付けられている既存の取り込みマッピングを変更します。

**構文**

`.alter``table`*テーブル名*`ingestion`*マッピングKind*`mapping`*マッピング名**マッピングフォーマット付きAsJson*

> [!NOTE]
> * このマッピングは、コマンドの一部として完全なマッピングを指定する代わりに、インジェス・コマンドによってその名前によって参照できます。
> * _マッピングの_有効な値は、 `CSV` `JSON`、 `avro` `parquet`、 `orc`、 、 、 、 、 です。

**例** 
 
```
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
| マッピング1 | CSV  | [{"名前":"行番号"、"データ型"、""""""""":null,"序数":0"定数":"null}、{"名前":"rowguid"、"データ型":"文字列"、"CsvDataType":"null"、オーディナル":1,"定数":null} |