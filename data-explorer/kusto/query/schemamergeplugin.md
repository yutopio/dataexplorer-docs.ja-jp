---
title: schema_merge プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの schema_merge プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 9a6f25a7ea1c75211d043fdbca7f99c16ff98e1b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250326"
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

## <a name="syntax"></a>構文

`T``|` `evaluate` `schema_merge(`*PreserveOrder*`)`

## <a name="arguments"></a>引数

* *PreserveOrder*: (省略可能) に設定すると `true` 、保持されている最初の表形式スキーマで定義されている列の順序を検証するようにプラグインに指示します。 同じ列が複数のスキーマに含まれている場合、列の序数は、その列が表示されている最初のスキーマの列の序数に似ている必要があります。 既定値は `true` です。

## <a name="returns"></a>戻り値

プラグインは、 `schema_merge` 演算子が返すものと同様の出力を返し [`getschema`](./getschemaoperator.md) ます。

## <a name="examples"></a>例

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
