---
title: パターンプラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの diffgram パターンプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0be0dc12f48723bc83376a36db04f764991f7f0d
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803099"
---
# <a name="diff-patterns-plugin"></a>diff パターンプラグイン

同じ構造体の2つのデータセットを比較し、2つのデータセットの違いを特徴付ける個別の属性 (ディメンション) のパターンを検出します。
 `Diffpatterns`は、エラーの分析を支援するために開発されています (たとえば、特定の期間内のエラーとエラーを比較することによって) が、同じ構造の2つのデータセット間で違いを検出する可能性があります。 

```kusto
T | evaluate diffpatterns(splitColumn)
```
> [!NOTE]
> `diffpatterns`は、大きなパターン (セット間でデータの差分の部分をキャプチャする) を検索することを目的としており、行単位の違いを示すものではありません。

## <a name="syntax"></a>構文

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

## <a name="arguments"></a>引数 

### <a name="required-arguments"></a>必須の引数

* SplitColumn - *column_name*

    クエリをデータ セットに分割する方法をアルゴリズムに指示します。 SplitValueA 引数と SplitValueB 引数 (下記参照) に指定した値に従って、アルゴリズムは、クエリを 2 つのデータ セット "A" と "B" に分割し、それらの相違点を分析します。 そのため、列を分割するには、少なくとも 2 つの個別の値が必要です。

* SplitValueA -*文字列*

    指定された SplitColumn 内の値のいずれかの文字列形式。 SplitColumn 内のこの値を持つすべての行は、データセット "A" と見なされます。

* SplitValueB -*文字列*

    指定された SplitColumn 内の値のいずれかの文字列形式。 SplitColumn 内のこの値を持つすべての行は、データセット "B" と見なされます。

    例: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

### <a name="optional-arguments"></a>省略可能な引数。

その他の引数はすべて省略できますが、下記と同じ順序で指定する必要があります。 既定値を使用することを示すには、文字列チルダ値 '~' を使用します (以下の例を参照)。

* WeightColumn - *column_name*

    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。 引数は数値列の名前にする必要があります (たとえば、、 `int` `long` 、 `real` )。
    weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
    
    例: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* しきい値-0.015 < *double* < 1 [既定値: 0.05]

    2 つのセット間の最小限のパターン (割合) の差を設定します。

    例: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions-0 < *int* [既定値: 無制限]

    結果パターンごとの非相関ディメンションの最大数を設定します。 制限を指定すると、クエリの実行時間が短縮されます。

    例: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard- *"任意-型の値"*

    現在のパターンにこの列に対する制限がないことを示す特定の種類のワイルドカード値を結果テーブルに設定します。
    既定値は null です。文字列の場合、既定値は空の文字列です。 既定値がデータ内で実行可能な値の場合は、別のワイルドカード値 (など) を使用する必要があり `*` ます。
    次の例をご覧ください。

    例: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="returns"></a>戻り値

`Diffpatterns`2つのセット内のデータのさまざまな部分を取得するパターンの小さなセットを返します (つまり、1つ目のデータセット内の行の大部分をキャプチャし、2番目のセットの行の割合を低くします)。 各パターンは、結果内の行によって表されます。

の結果は、 `diffpatterns` 次の列を返します。

* SegmentId: 現在のクエリのパターンに割り当てられた id です (注: Id は、繰り返しクエリで同じであることは保証されていません)。

* CountA: セット A のパターンによってキャプチャされた行の数 (セット A はと同じです `where tostring(splitColumn) == SplitValueA` )。

* CountB: セット B のパターンによってキャプチャされた行の数 (セット B はと同じです `where tostring(splitColumn) == SplitValueB` )。

* PercentA: パターンによってキャプチャされたセット内の行の比率 (100.0 * CountA/count (SetA))。

* PercentB: パターンによってキャプチャされたセット B 内の行の割合 (100.0 * CountB/count (SetB))。

* PercentDiffAB: A と B の絶対パーセント値の差 (|PercentA-PercentB |)は、2つのセットの違いを説明するパターンの有意性の主要な尺度です。

* 残りの列: 入力の元のスキーマであり、パターンについて説明します。各行 (パターン) は、列の非ワイルドカード値の交差部分を表します ( `where col1==val1 and col2==val2 and ... colN=valN` 行の各ワイルドカード以外の値に相当します)。

パターンごとに、パターンで設定されていない列 (つまり、特定の値に制限がない) には、ワイルドカード値が含まれます。これは、既定では null になります。 ワイルドカードを手動で変更する方法については、後述の「引数」セクションを参照してください。

* 注: 多くの場合、パターンは区別されません。 これらは重複している可能性があり、通常は元のすべての行に対応していません。 一部の行は、どのパターンにも当てはまらない場合があります。

> [!TIP]
> * 入力パイプで[where](./whereoperator.md)および[project](./projectoperator.md)を使用して、関心のあるものだけにデータを減らします。
> * 関心がある行が見つかったら、 `where` フィルターに特定の値を追加して、より詳しく調べることができます。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|セグメント ID|CountA|CountB|PercentA|PercentB|PercentDiffAB|State|EventType|ソース|DamageCrops|
|---|---|---|---|---|---|---|---|---|---|
|0|2278|93|49.8|7.1|42.7||ひょう||0|
|1|779|512|17.03|39.08|22.05||雷雨風|||
|2|1098|118|24.01|9.01|15|||訓練を受けた観測員|0|
|3|136|158|2.97|12.06|9.09|||新聞||
|4|359|214|7.85|16.34|8.49||鉄砲水|||
|5|50|122|1.09|9.31|8.22|アイオワ州||||
|6|655|279|14.32|21.3|6.98|||法執行機関||
|7|150|117|3.28|8.93|5.65||洪水|||
|8|362|176|7.91|13.44|5.52|||非常事態担当マネージャー||
