---
title: ビルドスキーマ() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのビルドスキーマ() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d0faceae970034ae96ced2b8958af4f16cb5dff4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517291"
---
# <a name="buildschema-aggregation-function"></a>ビルドスキーマ()(集計関数)

*DynamicExpr*のすべての値を受け入れた最小限のスキーマを返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`buildschema(`*ダイナミックExprの*要約`)`

**引数**

* *DynamicExpr*: 集計計算に使用される式。 パラメーターの列の型は`dynamic`にする必要があります。 

**戻り値**

グループ全体の*Expr*の最大値。

> [!TIP] 
> 構文`buildschema(json_column)`エラーが発生した場合:*動的オブジェクトではなく文字列`json_column`ですか?* その場合は、 を使用`buildschema(parsejson(json_column))`する必要があります。

**例**

入力列に次の 3 つの動的値があるとします。

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

結果のスキーマは次のようになります。

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

スキーマの内容は以下のとおりです。

* ルート オブジェクトは、4 つのプロパティ (x、y、z、t) を含むコンテナーです。
* "x" プロパティは "int" 型または "string" 型のいずれかになります。
* "y" プロパティは "double" 型、または "string" 型の "w" プロパティを含む別のコンテナーになります。
* ``indexer`` キーワードは、"z" と "t" が配列であることを示します。
* "z" 配列の各項目は int または string のいずれかです。
* "t" は string の配列です。
* すべてのプロパティは暗黙的に省略可能で、配列は空である可能性があります。

### <a name="schema-model"></a>スキーマ モデル

返されるスキーマの構文は次のとおりです。

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

これらは TypeScript 型のアノテーションのサブセットと同じで、Kusto の動的な値としてエンコードされます。 TypeScript のスキーマ例を以下に示します。

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }