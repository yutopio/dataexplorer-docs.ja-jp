---
title: schema_merge プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの schema_merge プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: b1f3ef10ac5cee3eb9bc1c1dca4c0de26bd85477
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780203"
---
# <a name="schema_merge-plugin"></a>schema_merge プラグイン

表形式スキーマ定義を統合スキーマにマージします。 

スキーマ定義は、演算子によって生成される形式であることが必要です [`getschema`](./getschemaoperator.md) 。

`schema merge`操作は入力スキーマの列を結合し、データ型を共通のデータ型に縮小しようとします。 データ型を減らすことができない場合は、問題のある列にエラーが表示されます。

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**構文**

`T``|` `evaluate` `schema_merge(`*PreserveOrder*`)`

**引数**

* *PreserveOrder*: (省略可能) に設定すると `true` 、保持されている最初の表形式スキーマで定義されている列の順序を検証するようにプラグインに指示します。 同じ列が複数のスキーマに含まれている場合、列の序数は、その列が表示されている最初のスキーマの列の序数に似ている必要があります。 既定値は `true` です。

**戻り値**

プラグインは、 `schema_merge` 演算子が返すものと同様の出力を返し [`getschema`](./getschemaoperator.md) ます。

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

異なる列順序を持つスキーマ ( `HttpStatus` 新しいバリアントのからへの序数変更) でマージ `1` `2` します。

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
|HttpStatus|-1|エラー (不明な CSL の種類: エラー (列が正しくありません))|エラー (列が順序どおりではありません)|

列の順序が異なるが、がに設定されているスキーマを使用してマージ `PreserveOrder` `false` します。

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
