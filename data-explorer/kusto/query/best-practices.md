---
title: クエリのベスト プラクティス - Azure Data Explorer
description: この記事では、Azure Data Explorer におけるクエリのベスト プラクティスについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 87154368a033afe1da7669e71e269081865b689d
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359915"
---
# <a name="query-best-practices"></a>クエリのベスト プラクティス

ここでは、クエリをより高速に実行するために従うべきいくつかのベスト プラクティスを示します。

|アクション  |用途  |使用禁止  |ノート  |
|---------|---------|---------|---------|
| **時間フィルター** | 最初に時間フィルターを使用する。 ||Kusto は、時間フィルターを使用するように、高度に最適化されています。| 
|**文字列演算子**      | `has` 演算子を使用します     | `contains` は使用しません     | 完全なトークンを検索する場合は、サブ文字列を検索しない `has` の方が有効に機能します。   |
|**大文字と小文字が区別される演算子**     |  `==` を使用します       | `=~` は使用しません       |  可能な場合は、大文字と小文字が区別される演算子を使用します。       |
| | `in` を使用します | `in~` は使用しません|
|  | `contains_cs` を使用します         | `contains` は使用しません        | `has`/`has_cs` を使用でき、`contains`/`contains_cs` を使用しないのであれば、その方が望ましいです。 |
| **検索テキスト**    |    特定の列を検索します     |    `*` は使用しません    |   `*` によって、すべての列に対してフル テキスト検索が行われます。    |
| **何百万行にわたる [動的オブジェクト](./scalar-data-types/dynamic.md)からフィールドを抽出する**    |  ほとんどのクエリによって、何百万行にわたる動的オブジェクトからフィールドが抽出される場合に、取り込み時に列を具体化します。      |         | この場合、列の抽出に対する支払いは一度だけになります。    |
| **[動的オブジェクト](./scalar-data-types/dynamic.md)のまれなキーおよび値の検索**    |  `MyTable | where DynamicColumn has "Rare value" | where DynamicColumn.SomeKey == "Rare value"` を使用します | `MyTable | where DynamicColumn.SomeKey == "Rare value"` を使用しないでください | これにより、ほとんどのレコードをフィルターで除外し、残りの部分のみの JSON 解析を実行します。 |
| **複数回使用する値を指定した `let` ステートメント** | [materialize() 関数](./materializefunction.md)を使用します |  |   `materialize()` の使用方法の詳細については、「[materialize()](materializefunction.md)」を参照してください。|
| **10 億件を超えるレコードに変換を適用する**| 変換に取り込まれるデータ量を減らすために、クエリを再形成します。| 避けられる場合は、大量のデータを変換しないでください。 | |
| **新しいクエリ** | `limit [small number]` または `count` を末尾に使用します。 | |     不明なデータ セットに対してバインドされていないクエリを実行すると、クライアントに返される結果がギガバイト規模で生じ、応答速度が低下して、ビジー状態のクラスターが発生する可能性があります。|
| **大文字と小文字が区別されない比較** | `Col =~ "lowercasestring"` を使用します | `tolower(Col) == "lowercasestring"` を使用しないでください |
| **既に小文字 (または大文字) になっているデータを比較する** | `Col == "lowercasestring"` (または `Col == "UPPERCASESTRING"`) | 大文字と小文字が区別されない比較の使用を避けてください。||
| **列のフィルター処理** |  テーブル列に対してフィルター処理を行います。|計算列に対してフィルター処理を行わないでください。 | |
| | `T | where predicate(<expression>)` を使用します | `T | extend _value = <expression> | where predicate(_value)` を使用しないでください ||
| **summarize 演算子** |  summarize 演算子の `group by keys` が高いカーディナリティを伴う場合には、[hint.strategy=shuffle](./shufflequery.md) を使用します。 | | 理想としては、高いカーディナリティとは 100 万を上回ります。|
|**[join 演算子](./joinoperator.md)** | 行数の少ないテーブルを選択して、最初に設定します (クエリの一番左)。 ||
| クラスター間で結合する |クラスター間で、ほとんどのデータが配置されている結合の "右" 側に対して、クエリを実行します。 ||
|左側の規模が小さく、右側の規模が大きい場合に結合する | [hint.strategy=broadcast](./broadcastjoin.md) を使用します || 小規模とは、最大 10 万件までのレコードのことです。 |
|両側の規模が大きすぎる場合に結合する | [hint.strategy=shuffle](./shufflequery.md) を使用します || 結合キーのカーディナリティが高い場合に使用します。|
|**同じ形式またはパターンを共有する文字列を含む列の値を抽出する**|  [parse 演算子](./parseoperator.md)を使用します | 複数の `extract()` ステートメントを使用しないでください。  | たとえば、`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` のような値です。
|**[extract() 関数](./extractfunction.md)**| 解析される文字列が同じ形式またはパターンに従っていない場合に、使用します。| |REGEX を使用して、必要な値を抽出します。|
| **[materialize() 関数](./materializefunction.md)** | 具体化されたデータ セットを減らすすべての演算子の候補をプッシュして、引き続きクエリのセマンティクスを維持します。 | |たとえば、フィルター処理するか、必要な列のみを射影します。

