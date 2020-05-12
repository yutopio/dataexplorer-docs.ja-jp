---
title: autocluster プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの autocluster プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b2d76c0dd7a3ee0724db67aa8cb0cd9229748fa
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225532"
---
# <a name="autocluster-plugin"></a>autocluster プラグイン

```kusto
T | evaluate autocluster()
```

AutoCluster は、データの個別の属性 (ディメンション) の一般的なパターンを見つけ出し、元のクエリの結果を (100 行であっても 100k 行であっても) 少数のパターンにまとめます。 AutoCluster はエラー (例外、クラッシュなど) を分析するために開発されましたが、フィルター処理された任意のデータ セットの操作にも使用できます。 

**構文**

`T | evaluate autocluster(`*引数*`)`

**戻り値**

AutoCluster は、通常はパターンの小さなセットを返します。これらのパターンは、複数の個別の属性にわたって共通する値を持つデータの一部をキャプチャします。 各パターンは、結果内の行によって表されます。 

最初の列はセグメント Id です。次の2つの列は、パターンによってキャプチャされた元のクエリからの行の数と割合を示します。 残りの列は、元のクエリにあったもので、それらの値は列の具体的な値か、変数値を意味するワイルド カード (既定では null) です。 

パターンは個別ではないことに注意してください。重複する可能性があり、通常は元のすべての行が含まれてはいません。 一部の行は、どのパターンにも当てはまらない場合があります。

> [!TIP]
> 入力パイプで[where](./whereoperator.md)および[project](./projectoperator.md)を使用して、関心のあるものだけにデータを減らします。
>
> 関心がある行が見つかったら、 `where` フィルターに特定の値を追加して、より詳しく調べることができます。

**引数 (すべて省略可能)**

`T | evaluate autocluster(`[*Sizeweight*、 *WeightColumn*、 *numseeds*、 *customwildcard*、 *customwildcard*、...]`)`

引数はすべて省略できますが、上記と同じ順序で指定する必要があります。 既定値を使用することを示すには、文字列チルダ値 '~' を使用します (以下の例を参照)。

使用可能な引数:

* SizeWeight-0 < *double* < 1 [既定値: 0.5]

    一般性 (高カバレッジ) と情報性 (多くの共有値) とのバランスをある程度制御できるようになります。 通常、この値を増やすと、パターン数が減り、各パターンはより大きい割合をカバーするようになります。 通常、この値を減らすと、より多くの共有値を持つ、より具体的なパターンが生成され、より小さい割合がカバーされます。 内部式の場合は、正規化された汎用スコアと、との重みを持つ情報スコアとの重み付け幾何平均です `SizeWeight` `1-SizeWeight` 。 

    例: `T | evaluate autocluster(0.8)`

* WeightColumn - *column_name*

    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。 引数は数値列の名前にする必要があります (例 : int、long、real)。 weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
    
    例: `T | evaluate autocluster('~', sample_Count)` 

* NumSeeds- *int* [既定値:25] 

    シード数によって、アルゴリズムの最初のローカル検索ポイント数が決まります。 データの構造によっては、シード数を増やすと検索領域が増えて、結果の数が増えたり品質が高くなったりしますが、クエリが遅くなるトレードオフがあります。 この値は両方の方向に低下しているので、5未満に減らすとパフォーマンスが低下することはほとんどなく、50を超えると追加のパターンが生成されることはほとんどありません。

    例: `T | evaluate autocluster('~', '~', 15)`

* CustomWildcard- *"any_value_per_type"*

    現在のパターンにこの列に対する制限がないことを示す特定の種類のワイルドカード値を結果テーブルに設定します。
    既定値は null です。文字列の場合、既定値は空の文字列です。 既定値がデータ内で実行可能な値の場合は、別のワイルドカード値を使用する必要があります (例: `*` )。

    例: `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|セグメント ID|Count|Percent|State|EventType|損害|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7||ひょう|NO
|1|512|8.7||雷雨風|YES
|2|898|15.3|テキサス州||

**カスタム ワイルドカードを使用した例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|セグメント ID|Count|Percent|State|EventType|損害|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7|\*|ひょう|NO
|1|512|8.7|\*|雷雨風|YES
|2|898|15.3|テキサス州|\*|\*

**参照**

* [basket](./basketplugin.md)
* [落とし](./reduceoperator.md)

**関連情報**

* AutoCluster は、「[Algorithms for Telemetry Data Mining using Discrete Attributes](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)」(分離属性を使用したテレメトリ データ マイニングのためのアルゴリズム) の Seed-Expand アルゴリズムにほぼ基づいています。 

