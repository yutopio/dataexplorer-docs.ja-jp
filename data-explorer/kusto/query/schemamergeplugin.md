---
title: schema_mergeプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーschema_mergeプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 67326a0e7a92d064613ee3a3de2851addb502fc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509250"
---
# <a name="schema_merge-plugin"></a>schema_mergeプラグイン

表形式スキーマ定義を統合スキーマにマージします。 

スキーマ定義は[、getschema](./getschemaoperator.md)演算子によって生成される形式であることが想定されます。

スキーマのマージ操作は、入力スキーマに表示される列を結合し、データ型を共通のデータ型に減らそうとします (データ型を減らすことができない場合は、問題のある列にエラーが表示されます)。

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**構文**

`T``|``evaluate`*PreserveOrder*保存`schema_merge(`順序`)`

**引数**

* *PreserveOrder*: (オプション)`true`に設定すると、最初の表形式スキーマで定義されている列の順序が保持されることを検証するようにプラグインに指示します。 つまり、複数のスキーマに同じ列が含まれている場合、列序数は最初に出現したスキーマと同じである必要があります。 既定値は `true` です。

**戻り値**

プラグイン`schema_merge`は[、getschema](./getschemaoperator.md)演算子が返すものに出力シミアーを返します。

**使用例**

新しい列が追加されたスキーマとマージします。

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*結果*

|ColumnName | ColumnOrdinal | DataType | [列の型]|
|---|---|---|---|
|Uri|0|System.String|string|
|HttpStatus|1|System.Int32|INT|
|Referrer|2|System.String|string|

異なる列の順序 (新しいバリアント`HttpStatus`の順序の変更`1`) を`2`持つスキーマとマージします。

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*結果*

|ColumnName | ColumnOrdinal | DataType | [列の型]|
|---|---|---|---|
|Uri|0|System.String|string|
|Referrer|1|System.String|string|
|HttpStatus|-1|エラー(不明な CSL 型:ERROR(列が順序付けられていません))|エラー(列が順序どおりになっている)|

異なる列の順序を持つスキーマをマージします`PreserveOrder`が、`false`次の時刻に設定します。

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*結果*

|ColumnName | ColumnOrdinal | DataType | [列の型]|
|---|---|---|---|
|Uri|0|System.String|string
|Referrer|1|System.String|string
|HttpStatus|2|System.Int32|INT|