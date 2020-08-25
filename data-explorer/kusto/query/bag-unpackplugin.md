---
title: bag_unpack プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bag_unpack プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 48670f1c0bb38599451bc73afa58d6f1dba344a2
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793644"
---
# <a name="bag_unpack-plugin"></a>bag_unpack プラグイン

`bag_unpack`プラグインは `dynamic` 、各プロパティバッグの最上位レベルスロットを列として扱うことにより、型の単一の列をアンパックします。

```kusto
T | evaluate bag_unpack(col1)
```

## <a name="syntax"></a>構文

*T* `|` `evaluate` `bag_unpack(` *列* [ `,` *outputcolumnprefix* ] [列の `,` *競合* ] [ `,` *ignoredproperties* ] `)`

## <a name="arguments"></a>引数

* *T*: 列 *列* がアンパックされる表形式の入力。
* *列*: アンパックする *T* の列。 `dynamic` 型であることが必要です。
* *Outputcolumnprefix*: プラグインによって生成されるすべての列に追加する共通プレフィックス。 この引数は省略可能です。
* 列の*競合*: 列の競合解決の方向です。 この引数は省略可能です。 引数を指定する場合は、次のいずれかの値に一致する文字列リテラルである必要があります。
    - `error` -Query によってエラー (既定値) が生成されます。
    - `replace_source` -ソース列が置換されます
    - `keep_source` -ソース列は保持されます
* *Ignoredproperties*: 省略可能なバッグプロパティのセットを無視します。 引数を指定する場合、 `dynamic` 1 つまたは複数の文字列リテラルを含む配列の定数である必要があります。

## <a name="returns"></a>戻り値

このプラグインは、表 `bag_unpack` 形式の入力 (*T*) と同数のレコードを含むテーブルを返します。 テーブルのスキーマは、表形式入力のスキーマと同じであり、次のように変更されます。

* 指定された入力列 (*列*) は削除されます。
* スキーマは、 *T*の最上位レベルのプロパティバッグの値に個別のスロットがあるのと同じ数の列で拡張されます。各列の名前は、各スロットの名前に対応します。オプションで *Outputcolumnprefix*が付加されます。 同じスロットのすべての値が同じ型である場合、または型の値が異なる場合は、その型がスロットの型になり `dynamic` ます。

**ノート**

プラグインの出力スキーマはデータ値に依存しているため、データ自体として "予測不可能" としています。 異なるデータ入力を使用してプラグインを複数回実行すると、異なる出力スキーマが生成される可能性があります。

プラグインへの入力データは、出力スキーマが表形式スキーマのすべての規則に従うようにする必要があります。 特に次の点に違いがあります。

* 出力列の名前を、テーブル入力 *t*の既存の列と同じにすることはできません。ただし、その列がアンパック (*列*) である場合は、同じ名前の2つの列が生成されるためです。

* すべてのスロット名の先頭に *Outputcolumnprefix*が付いている場合は、有効なエンティティ名であり、 [識別子の名前付け規則](./schema-entities/entity-names.md#identifier-naming-rules)に従っている必要があります。

## <a name="examples"></a>例

### <a name="expand-a-bag"></a>バッグを展開する


<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d)
```

|名前  |Age|
|------|---|
|John  |20 |
|Dave  |40 |
|Jasmine|30 |


### <a name="expand-a-bag-with-outputcolumnprefix"></a>OutputColumnPrefix を使用してバッグを展開する

バッグを展開し、オプションを使用して、 `OutputColumnPrefix` プレフィックス ' Property_ ' で始まる列名を生成します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, 'Property_')
```

|Property_Name|Property_Age|
|---|---|
|John|20|
|Dave|40|
|Jasmine|30|

### <a name="expand-a-bag-with-columnsconflict"></a>[コラムの競合] があるバッグを展開する

バッグを展開し、オプションを使用し `columnsConflict` て、演算子によって作成された既存の列と列の間の競合を解決し `bag_unpack()` ます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='replace_source') // Use new name
```

|名前|Age|
|---|---|
|John|20|
|Dave|40|
|Jasmine|30|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='keep_source') // Keep old name
```

|名前|Age|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

### <a name="expand-a-bag-with-ignoredproperties"></a>IgnoredProperties を含むバッグを展開する

バッグを展開し、オプションを使用して `ignoredProperties` プロパティバッグの特定のプロパティを無視します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20, "Address": "Address-1" }),
    dynamic({"Name": "Dave", "Age":40, "Address": "Address-2"}),
    dynamic({"Name": "Jasmine", "Age":30, "Address": "Address-3"}),
]
// Ignore 'Age' and 'Address' properties
| evaluate bag_unpack(d, ignoredProperties=dynamic(['Address', 'Age']))
```

|名前|
|---|
|John|
|Dave|
|Jasmine|
