---
title: .show インジェスション マッピング - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのインジェスション マッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 91990fe40664ae89d69357812b0def2c7288eb7d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519824"
---
# <a name="show-ingestion-mappings"></a>.show インジェスション マッピング

インジェクション マッピング (すべてまたは名前で指定されたもの) を表示します。

* `.show``table`*テーブル名*`ingestion`*マッピングキンド*  `mappings`

* `.show``table`*テーブル名*`ingestion`*マッピングKind* `mapping` *マッピング名*   

すべてのマッピングの種類からすべての取り込みマッピングを表示します。

* `.show``table`*テーブル名*`ingestion`  `mapping`
 
**例** 
 
```
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**出力例**

| 名前     | 種類 | マッピング     |
|----------|------|-------------|
| マッピング1 | CSV  | [{"名前":"行番号"、"データ型"、""""""""":null,"序数":0"定数":"null}、{"名前":"rowguid"、"データ型":"文字列"、"CsvDataType":"null"、オーディナル":1,"定数":null} |