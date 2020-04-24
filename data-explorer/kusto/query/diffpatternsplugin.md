---
title: diffpatterns プラグイン - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの diffpatterns プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fc4d1c7441e02eeeb1d90e1f8c0f2a521e3793da
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515999"
---
# <a name="diffpatterns-plugin"></a>拡散パターンプラグイン

```kusto
T | evaluate diffpatterns(splitColumn)
```
同じ構造の 2 つのデータ・セットを比較し、2 つのデータ・セット間の相違を特徴づける不連続属性 (ディメンション) のパターンを検索します。 diffpatterns は、エラーの分析を支援する (たとえば、所定の期間内のエラーと非エラーを比較する) ために開発されましたが、同じ構造の 2 つの任意のデータ セット間の差異も見つけることができます。 

**構文**

`T | evaluate diffpatterns(`スプリットカラム、スプリットバリューA、スプリットバリューB [、重量列、しきい値、最大寸法、カスタムワイルドカード、.]]`)` 

**必須の引数**

* SplitColumn - *column_name*

    クエリをデータ セットに分割する方法をアルゴリズムに指示します。 SplitValueA 引数と SplitValueB 引数 (下記参照) に指定した値に従って、アルゴリズムは、クエリを 2 つのデータ セット "A" と "B" に分割し、それらの相違点を分析します。 そのため、列を分割するには、少なくとも 2 つの個別の値が必要です。

* SplitValueA -*文字列*

    指定された SplitColumn 内の値のいずれかの文字列形式。 SplitColumn にこの値がある行はすべてデータ セット “A” と見なされます。

* SplitValueB -*文字列*

    指定された SplitColumn 内の値のいずれかの文字列形式。 SplitColumn 内でこの値を持つすべての行は、データ セット "B" と見なされます。

    例 : `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**省略可能な引数**

その他の引数はすべて省略できますが、下記と同じ順序で指定する必要があります。 既定値を使用することを示すには、文字列チルダ値 '~' を使用します (以下の例を参照)。

* WeightColumn - *column_name*

    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。 引数は数値列の名前にする必要があります (例 : int、long、real)。
    weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
    
    例 : `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* しきい値 - 0.015 <*ダブル*< 1 [デフォルト: 0.05]

    2 つのセット間の最小限のパターン (割合) の差を設定します。

    例: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* 最大寸法 - 0 < *int* [デフォルト: 無制限]

    結果パターンごとの非相関ディメンションの最大数を設定します。制限を指定するとクエリの実行時間が短くなります。

    例: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* カスタムワイルドカード - *"型ごとの任意の値"*

    現在のパターンにこの列に対する制限がないことを示す特定の種類のワイルドカード値を結果テーブルに設定します。
    既定値は null です。文字列の場合、既定値は空の文字列です。 デフォルトがデータ内の有効な値である場合は、別のワイルドカード値を使用する必要があります (例`*`えば)。
    次の例をご覧ください。

    例 : `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

**戻り値**

diffpatterns は、2 つのセット内の異なる部分のデータをキャプチャするパターン (つまり、1 つ目のデータ セットの大きな割合の行と 2 つ目のデータ セットの小さな割合の行をキャプチャするパターン) の、通常は小さいセットを返します。 各パターンは、結果内の行によって表されます。

diffpatterns の結果は、次の列を返します。

* SegmentId: 現在のクエリのパターンに割り当てられた ID (注: 繰り返しクエリでは ID が同じであるとは保証されません)。

* CountA: セット A (セット A は`where tostring(splitColumn) == SplitValueA`) のパターンによってキャプチャされた行数です。

* CountB: セット B (セット B は と同等) のパターン`where tostring(splitColumn) == SplitValueB`によってキャプチャされた行数。

* PercentA: パターンによってキャプチャされたセット A の行の割合 ( 100.0 * CountA / count (SetA) )。

* パーセントB: パターンによってキャプチャされたセット B の行の割合 ( 100.0 * CountB / count (SetB) )。

* パーセントディアブ: A と B の絶対パーセントポイント差 ( |パーセントA - パーセントB|)は、2 つのセット間の差を記述する際のパターンの重要度の主な尺度です。

* 残りの列: は入力の元のスキーマであり、パターンを記述し、各行 (パターン) は列の非ワイルドカード値の交点を再送します (`where col1==val1 and col2==val2 and ... colN=valN`行の非ワイルドカード値に相当します)。

パターンごとに、パターンに設定されていない列 (特定の値に制限がない) には、デフォルトで null であるワイルドカード値が含まれます (ワイルドカードを手動で変更する方法については、「引数」セクションを参照)。

* 注: パターンは通常は区別されず、重なり合っていて、通常は元の行のすべてがカバーされません。 一部の行は、どのパターンにも当てはまらない場合があります。


**ヒント**

入力パイプの[場所](./whereoperator.md)と[プロジェクト](./projectoperator.md)を使用して、データを目的のデータだけに減らします。

関心がある行が見つかったら、 `where` フィルターに特定の値を追加して、より詳しく調べることができます。

* 注: diffpatterns は、(セット間のデータの差の部分をキャプチャする)重要なパターンを見つけることを目的としており、行ごとの違いのためには意図されていません。

**例**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```
|セグメント ID|CountA|CountB|PercentA|PercentB|PercentDiffAB|State|EventType|source|DamageCrops|
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

