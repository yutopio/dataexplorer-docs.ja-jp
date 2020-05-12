---
title: bag_unpack プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bag_unpack プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: fd8b0968d90f1c5239cae80c3be9c2a32d0603d6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225498"
---
# <a name="bag_unpack-plugin"></a>bag_unpack プラグイン

プラグインは、 `bag_unpack` `dynamic` 各プロパティバッグの最上位レベルスロットを列として扱うことで、型の単一の列をアンパックします。

    T | evaluate bag_unpack(col1)

**構文**

*T* `|` `evaluate` `bag_unpack(` *列* `,` [ *outputcolumnprefix* ]`)`

**引数**

* *T*: 列*列*がアンパックされる表形式の入力。
* *列*: アンパックする*T*の列。 `dynamic` 型であることが必要です。
* *Outputcolumnprefix*: プラグインによって生成されるすべての列に追加する共通プレフィックス。
  省略可能。

**戻り値**

このプラグインは、表 `bag_unpack` 形式の入力 (*T*) と同数のレコードを含むテーブルを返します。 テーブルのスキーマは、次のように変更された表形式の入力と同じです。

* 指定された入力列 (*列*) は削除されます。

* スキーマは、 *T*の最上位レベルのプロパティバッグの値に個別のスロットがあるのと同じ数の列で拡張されます。各列の名前は、各スロットの名前に対応します。オプションで*Outputcolumnprefix*が付加されます。 この型は、スロットの型 (同じスロットのすべての値が同じ型である場合)、または `dynamic` (型の値が異なる場合は) のいずれかです。

**メモ**

プラグインの出力スキーマはデータ値に依存しているため、データ自体として "予測不可能" としています。 そのため、データ入力が異なるプラグインを複数回実行すると、異なる出力スキーマが生成される可能性があります。

プラグインへの入力データは、出力スキーマが表形式スキーマのすべての規則に準拠している必要があります。 特に次の点に違いがあります。

1. 出力列の名前を、テーブル入力*T*の既存の列と同じにすることはできません。ただし、列をアンパック (*列*) する場合は、同じ名前の2つの列が生成されます。

2. すべてのスロット名の先頭に*Outputcolumnprefix*が付いている場合は、有効なエンティティ名であり、[識別子の名前付け規則](./schema-entities/entity-names.md#identifier-naming-rules)に準拠している必要があります。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Smitha", "Age":30}),
]
| evaluate bag_unpack(d)
```

|名前  |Age|
|------|---|
|John  |20 |
|Dave  |40 |
|Smitha|30 |
