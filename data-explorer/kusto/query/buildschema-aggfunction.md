---
title: buildschema () (集計関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの buildschema () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3fb6ae643bb4350cea1ffef4493625bc9c7d191d
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793570"
---
# <a name="buildschema-aggregation-function"></a>buildschema () (集計関数)

*Dynamicexpr*のすべての値を制御する最小スキーマを返します。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`buildschema(` *dynamicexpr*の概要`)`

## <a name="arguments"></a>引数

* *Dynamicexpr*: 集計計算に使用される式です。 パラメーターの列の型はである必要があり `dynamic` ます。 

## <a name="returns"></a>戻り値

グループ全体のの最大値 *`Expr`* 。

> [!TIP] 
> `buildschema(json_column)`で構文エラーが発生する場合は、次のようになります。
>
> > *`json_column`動的オブジェクトではなく文字列ですか?*
>
> 次に、を使用し `buildschema(parsejson(json_column))` ます。

## <a name="example"></a>例

入力列に3つの動的な値が含まれているとします。

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

結果のスキーマは次のようになります。

```kusto
{ 
    "x":["int", "string"],
    "y":["double", {"w": "string"}],
    "z":{"`indexer`": ["int", "string"]},
    "t":{"`indexer`": "string"}
}
```

スキーマの内容は以下のとおりです。

* ルートオブジェクトは、x、y、z、t という名前の4つのプロパティを持つコンテナーです。
* "X" という名前のプロパティは、型 "int" または型 "string" である可能性があります。
* "Y" という名前のプロパティ。 "double" 型の場合もあれば、"string" 型の "w" というプロパティを持つ別のコンテナーの場合もあります。
* ``indexer`` キーワードは、"z" と "t" が配列であることを示します。
* 配列 "z" 内の各項目は、"int" 型または "string" 型です。
* "t" は string の配列です。
* すべてのプロパティは暗黙的に省略可能で、配列は空である可能性があります。

### <a name="schema-model"></a>スキーマ モデル

返されるスキーマの構文は次のとおりです。

```output
Container ::= '{' Named-type* '}';
Named-type ::= (name | '"`indexer`"') ':' Type;
Type ::= Primitive-type | Union-type | Container;
Union-type ::= '[' Type* ']';
Primitive-type ::= "int" | "string" | ...;
```

値は、Kusto 動的な値としてエンコードされた TypeScript 型の注釈のサブセットに相当します。 TypeScript のスキーマ例を以下に示します。

```typescript
var someobject: 
{
    x?: (number | string),
    y?: (number | { w?: string}),
    z?: { [n:number] : (int | string)},
    t?: { [n:number]: string }
}
```
