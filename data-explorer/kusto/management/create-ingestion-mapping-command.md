---
title: .create インジェスション マッピング - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .create インジェスション マッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 10e656b074516ad8b0018e627d9904251aebbf10
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744498"
---
# <a name="create-ingestion-mapping"></a>.create インジェスト マッピング

特定のテーブルと特定の形式に関連付けられた取り込みマッピングを作成します。

**構文**

`.create``table`*テーブル名*`ingestion`*マッピングKind*`mapping`*マッピング名**マッピングフォーマット付きAsJson*

> [!NOTE]
> * 作成後は、コマンドの一部として完全なマッピングを指定する代わりに、マッピングをインジェスション・コマンド内でその名前で参照できます。
> * _マッピングの_有効な値は、 `CSV` `JSON`、 `avro` `parquet`、 、 、`orc`
> * 同じ名前のマッピングがテーブルに既に存在する場合は、次の手順を実行します。
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
| マッピング1 | CSV  | [{"名前":"行番号"、"データ型"、""""""""":null,"序数":0"定数":"null}、{"名前":"rowguid"、"データ型":"文字列"、"CsvDataType":"null"、オーディナル":1,"定数":null} |
