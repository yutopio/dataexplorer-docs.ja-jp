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
ms.openlocfilehash: 1823a9d875c6294f360fbce77fcb5e1d2c968019
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818552"
---
# <a name="bag_unpack-plugin"></a>bag_unpack プラグイン

プラグインは、 `bag_unpack` `dynamic` 各プロパティバッグの最上位レベルスロットを列として扱うことで、型の単一の列をアンパックします。

    T | evaluate bag_unpack(col1)

**構文**

*T* `|` `evaluate` `bag_unpack(` *列*[ `,` *outputcolumnprefix* ] [列の結合 `,` *columnsConlict* ] [ `,` *ignoredproperties* ]`)`

**引数**

* *T*: 列*列*がアンパックされる表形式の入力。
* *列*: アンパックする*T*の列。 `dynamic` 型であることが必要です。
* *Outputcolumnprefix*: プラグインによって生成されるすべての列に追加する共通プレフィックス。 この引数は省略可能です。
* 列の競合解決の方向*です。* この引数は省略可能です。 引数を指定する場合は、次のいずれかの値に一致する文字列リテラルである必要があります。
    - `error`-query によってエラー (既定値) が生成されます。
    - `replace_source`-ソース列が置換されます
    - `keep_source`-ソース列は保持されます
* *Ignoredproperties*: 省略可能なバッグプロパティのセットを無視します。 引数を指定する場合、 `dynamic` 1 つまたは複数の文字列リテラルを含む配列の定数である必要があります。

**戻り値**

このプラグインは、表 `bag_unpack` 形式の入力 (*T*) と同数のレコードを含むテーブルを返します。 テーブルのスキーマは、表形式入力のスキーマと同じであり、次のように変更されます。

* 指定された入力列 (*列*) は削除されます。

* スキーマは、 *T*の最上位レベルのプロパティバッグの値に個別のスロットがあるのと同じ数の列で拡張されます。各列の名前は、各スロットの名前に対応します。オプションで*Outputcolumnprefix*が付加されます。 この型は、スロットの型 (同じスロットのすべての値が同じ型である場合)、または `dynamic` (型の値が異なる場合は) のいずれかです。

**ノート**

プラグインの出力スキーマはデータ値に依存しているため、データ自体として "予測不可能" としています。 そのため、異なるデータ入力を使用してプラグインを複数回実行すると、異なる出力スキーマが生成される可能性があります。

プラグインへの入力データは、出力スキーマが表形式スキーマのすべてのルールに従っている必要があります。 特に次の点に違いがあります。

1. 出力列の名前を、テーブル入力*T*の既存の列と同じにすることはできません。ただし、列をアンパック (*列*) する場合は、同じ名前の2つの列が生成されます。

2. すべてのスロット名の先頭に*Outputcolumnprefix*が付いている場合は、有効なエンティティ名であり、[識別子の名前付け規則](./schema-entities/entity-names.md#identifier-naming-rules)に従っている必要があります。

**使用例**

バッグを展開する:

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

バッグを展開し、オプションを使用して `OutputColumnPrefix` プレフィックス ' Property_ ' で始まる列名を生成します。

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

バッグを展開し、オプションを使用して `columnsConlict` 、演算子によって作成された既存の列と列の間の競合を解決し `bag_unpack()` ます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConlict='replace_source') // Use new name
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
| evaluate bag_unpack(d, columnsConlict='keep_source') // Keep old name
```

|名前|Age|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

バッグを展開し、オプションを使用して `ignoredProperties` 、プロパティバッグに存在する特定のプロパティを無視します。

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
