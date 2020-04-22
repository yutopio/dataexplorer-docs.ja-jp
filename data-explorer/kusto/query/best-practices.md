---
title: クエリのベスト プラクティス - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリのベスト プラクティスについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 3458c2fcd7fab3345f65393d7e9a13e844088a9f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663870"
---
# <a name="query-best-practices"></a>クエリのベスト プラクティス 

## <a name="general"></a>全般

クエリの実行速度を上げるために、"Dos and Don'ts" がいくつか用意されています。

### <a name="do"></a>次のことを行います

*   最初に時間フィルターを使用する。 Kustoは時間フィルターを利用するために高度に最適化されています。
*   文字列演算子を使用する場合:
    *   完全`has`なトークン`contains`を探すときは、演算子を優先します。 `has`サブストリングを検索する必要がなされないので、よりパフォーマンスが高くなります。
    *   よりパフォーマンスが高いため、大文字と小文字を区別する演算子を使用する方が適しています。 たとえば、 、`==`を`=~``in``in~`超えて、および`contains_cs``contains`オーバー を使用する`contains`/`contains_cs`(ただし、`has`/`has_cs`完全に避けて使用できる場合は、さらに優れています)。
*   (すべての列で全文検索)`*`を使用するよりも、特定の列を検索する方が好き
*   クエリの大半が数百万行にわたる[動的オブジェクト](./scalar-data-types/dynamic.md)からの抽出フィールドを扱っている場合は、インジェスト時にこの列を具体化することを検討してください。 この方法では、列の抽出に対して 1 回だけ支払います。  
*   複数回使用する値`let`をステートメントがある場合は[、materialize() 関数](./materializefunction.md)の使用を検討してください ( の使用[best practices](#materialize-function)`materialize()`に関するベスト プラクティスを参照してください )。

### <a name="dont"></a>次のことは行わないでください

*   新しいクエリを実行せずに`limit [small number]`、最後`count`にクエリを実行しないでください。
    未知のデータ セットに対して非バインド クエリを実行すると、結果の GB がクライアントに返され、応答が遅くなり、クラスターがビジー状態になる場合があります。
*   10 億件を超えるレコードの変換 (JSON、文字列など) を適用している場合は、変換に渡されるデータ量を減らすためにクエリの形を変更します。
*   大文字と小文字を`tolower(Col) == "lowercasestring"`区別しない比較を行う場合は使用しないでください。 代わりに `Col =~ "lowercasestring"` を使用してください。
    *   データが既に小文字 (または大文字) の場合は、大文字小文字を区別しない比較を使用しないように`Col == "lowercasestring"`し、`Col == "UPPERCASESTRING"`代わりに ( または ) を使用します。
*   テーブルの列にフィルタを適用できる場合は、集計列にフィルタを適用しないでください。 言い換えれば、`T | extend _value = <expression> | where predicate(_value)`の代わりに`T | where predicate(<expression>)`、 を行います: .

## <a name="summarize-operator"></a>summarize 演算子

*   集計演算子のグループバイキーが高いカーディナリティ (ベストプラクティス: 100 万を超える) である場合は、 [hint.strategy= shuffle](./shufflequery.md)を使用することをお勧めします。

## <a name="join-operator"></a>join 演算子

*   join[演算子](./joinoperator.md)を使用する場合は、行数が少ないテーブルを選択して最初の行 (左端) にします。 
*   クラスター間[で結合演算子](./joinoperator.md)データを使用する場合は、結合の 「右側」側 (ほとんどのデータが存在する場所) でクエリを実行します。
*   左側が小さく (最大 100,000 レコード) で、右側が大きい場合は、 [hint.strategy=broadcast](./broadcastjoin.md)を使用します。
*   結合の両側が大きすぎて、結合キーが高いカーディナリティを持つ場合は[、hint.strategy= shuffle](./shufflequery.md)を使用します。
    
## <a name="parse-operator-and-extract-function"></a>解析演算子と抽出() 関数

*   [解析演算子](./parseoperator.md)(単純モード) は、ターゲット列の値に、すべて同じ形式またはパターンを共有する文字列が含まれている場合に便利です。
たとえば、 のような`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`値を持つ列の場合、各フィールドの値を抽出する場合は`parse`、複数`extract()`のステートメントではなく演算子を使用します。
*   [extract() 関数](./extractfunction.md)は、解析された文字列がすべて同じ形式またはパターンに従っていない場合に便利です。
このような場合は、REGEX を使用して必要な値を抽出します。

## <a name="materialize-function"></a>マテリアライズ()関数

*   [materialize() 関数](./materializefunction.md)を使用する場合は、マテリアライズされたデータ・セットを削減し、クエリのセマンティクスを維持する可能性のある演算子をすべてプッシュしてみてください。 たとえば、フィルタやプロジェクトの必要な列のみなどです。
    
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

* テキストのフィルタは相互に使用でき、マテリアライズ式にプッシュできます。
    クエリには`Timestamp`これらの列 、、`Text``Resource1`および`Resource2`マテリアライズ式内の列を射出することをお勧めします。
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   フィルタがこのクエリと同じでない場合:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   次のクエリのように、マテリアライズされた結果に対して両方のフィルタを論理的`or`な式で結合する価値があります(組み合わされたフィルタによってマテリアライズされた結果が大幅に減少する場合)。 ただし、クエリのセマンティクスを保持するために、各共用体のレッグにフィルターを保持します。
     
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
    