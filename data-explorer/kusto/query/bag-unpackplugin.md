---
title: bag_unpackプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーbag_unpackプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 374ff3ca8101e24a53926a78a1b8781447f32dbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518226"
---
# <a name="bag_unpack-plugin"></a>bag_unpackプラグイン

プラグイン`bag_unpack`は、各プロパティバッグのトップレベルスロット`dynamic`を列として扱うことで、型の単一列を解凍します。

    T | evaluate bag_unpack(col1)

**構文**

*T* `|` T`evaluate``,`*Column*列 [*出力列プレフィックス*] `bag_unpack(``)`

**引数**

* *T*:*列列*をアンパックする表形式の入力。
* *列*: 解凍する*T*の列。 `dynamic` 型であることが必要です。
* *出力列プレフィックス*: プラグインによって生成されるすべての列に追加する共通のプレフィックス。
  省略可能。

**戻り値**

プラグイン`bag_unpack`は、表形式の入力 (*T*) と同じ数のレコードを持つテーブルを返します。 テーブルのスキーマは、次の変更を加えた表形式の入力と同じです。

* 指定した入力列 (*列*) が削除されます。

* T の最上位のプロパティ バッグ値にそれぞれ異なるスロットがあるのと同じ*数の列*でスキーマが拡張されます。各列の名前は、各スロットの名前に対応し、オプションで*OutputColumnPrefix*のプレフィックスを付けます。 そのタイプは、スロットのタイプ (同じスロットのすべての値が同じタイプの場合) か`dynamic`(値のタイプが異なる場合) のいずれかです。

**メモ**

プラグインの出力スキーマはデータ値に依存し、データ自体と同じように「予測不能」になります。 したがって、異なるデータ入力を持つプラグインの複数の実行は、異なる出力スキーマを生成可能性があります。

プラグインへの入力データは、出力スキーマが表形式スキーマのすべての規則に準拠するようにする必要があります。 特に次の点に違いがあります。

1. 出力列名は、同じ名前の 2 つの列を生成するので、アンパックされる列 (*Column*) 自体でない限り、表形式入力*T*の既存の列と同じにすることはできません。

2. すべてのスロット名は、 *OutputColumnPrefix*のプレフィックスとして付けられる場合、有効なエンティティ名で、[識別子の名前付け規則](./schema-entities/entity-names.md#identifier-naming-rules)に準拠している必要があります。

**例**

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
|スミサ|30 |