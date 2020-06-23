---
title: クエリのベストプラクティス-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのクエリのベストプラクティスについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e6db181188250d28689cca64762e13973f2f33f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128820"
---
# <a name="best-practices"></a>ベスト プラクティス 

## <a name="general"></a>全般

いくつかの "Dos and すべき" を使用すると、クエリの実行速度を向上させることができます。

### <a name="do"></a>次のことを行います

*   最初に時間フィルターを使用する。 Kusto は、時間フィルターを利用するように高度に最適化されています。
*   文字列演算子を使用する場合:
    *   `has` `contains` 完全なトークンを検索する場合は、演算子を優先します。 `has`は、部分文字列を検索する必要がないため、パフォーマンスが向上します。
    *   必要に応じて大文字と小文字を区別する演算子を使用することをお勧めします。 たとえば、 `==` over、over、および over を使用することをお勧めし `=~` `in` `in~` `contains_cs` `contains` ます (ただし、全体を回避して使用する場合は `contains` / `contains_cs` `has` / `has_cs` さらに優れています)。
*   `*`(すべての列に対してフルテキスト検索) を使用するのではなく、特定の列を検索する
*   大量の行にわたる[動的オブジェクト](./scalar-data-types/dynamic.md)からフィールドを抽出するクエリのほとんどがある場合は、インジェスト時にこの列を具体化することを検討してください。 この方法では、列の抽出に1回だけ料金がかかります。  
*   ステートメントの値が複数回使用されている場合は、 `let` [具体化 () 関数](./materializefunction.md)の使用を検討してください (を使用する際の[ベストプラクティス](#materialize-function)を参照してください `materialize()` )。

### <a name="dont"></a>次のことは行わないでください

*   新しいクエリ `limit [small number]` は、または最後には使用しないでください `count` 。
    不明なデータセットに対してバインドされていないクエリを実行すると、クライアントに対して Gb の結果が返され、応答が遅くなり、クラスターがビジー状態になる可能性があります。
*   10億レコードを超える変換 (JSON、文字列など) を適用していることがわかった場合は、変換に取り込むデータの量を減らすためにクエリをリシェイプします。
*   `tolower(Col) == "lowercasestring"`大文字と小文字を区別しない比較の実行には使用しないでください。 代わりに `Col =~ "lowercasestring"` を使用してください。
    *   データが既に小文字 (または大文字) になっている場合は、大文字と小文字を区別しない比較を使用しないで `Col == "lowercasestring"` ください。代わりに、(または) を使用して `Col == "UPPERCASESTRING"` ください。
*   テーブル列でフィルター処理できる場合は、計算列に対してフィルター処理を行わないでください。 つまり、の代わりに `T | extend _value = <expression> | where predicate(_value)` 、を実行 `T | where predicate(<expression>)` します。

## <a name="summarize-operator"></a>summarize 演算子

*   集計演算子の group by キーのカーディナリティが高い場合 (ベストプラクティス: 100万を超える)、ヒントを使用することをお勧め[します。方法 = シャッフル](./shufflequery.md)。

## <a name="join-operator"></a>join 演算子

*   [Join 演算子](./joinoperator.md)を使用する場合は、最初の行よりも小さい行を含むテーブルを選択します (一番左)。 
*   クラスター間で[結合演算子](./joinoperator.md)データを使用する場合は、結合の "右側" 側でクエリを実行します (ほとんどのデータが配置されています)。
*   左側が小さく (最大10万レコード)、右側が大きい場合は、ヒントを使用[します。方法 = ブロードキャスト](./broadcastjoin.md)。
*   結合の両側が大きすぎて、結合キーのカーディナリティが高い場合は、ヒントを使用[します。方法 = シャッフル](./shufflequery.md)。
    
## <a name="parse-operator-and-extract-function"></a>parse operator and extract () 関数

*   [parse 演算子](./parseoperator.md)(simple モード) は、ターゲット列の値に、すべて同じ形式またはパターンを共有する文字列が含まれている場合に便利です。
たとえば、などの値を持つ列の場合、 `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` 各フィールドの値を抽出するときに、 `parse` 複数のステートメントの代わりに演算子を使用し `extract()` ます。
*   [extract () 関数](./extractfunction.md)は、解析された文字列がすべて同じ形式またはパターンに従っていない場合に便利です。
このような場合は、REGEX を使用して必要な値を抽出します。

## <a name="materialize-function"></a>具体化 () 関数

*   [具体化 () 関数](./materializefunction.md)を使用する場合は、具体化されたデータセットを減らすことができるすべての可能な演算子をプッシュし、引き続きクエリのセマンティクスを維持します。 たとえば、フィルターを使用するか、必要な列のみを射影します。
    
    **例:**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* テキストのフィルターは相互に適用され、具体化式にプッシュできます。
    クエリに必要なのは、、、およびの各列のみである `Timestamp` `Text` ため、 `Resource1` `Resource2` 具体化された式の中でこれらの列を射影することをお勧めします。
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   次のクエリのようにフィルターが同じでない場合:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   次のクエリのように、具体化された結果に対する両方のフィルターを論理式によって結合することは、(結合フィルターによって具体化された結果を劇的に減らす場合) 意味を持つ場合があり `or` ます。 ただし、クエリのセマンティクスを維持するために、各共用体区間にフィルターを保持します。
     
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```
    