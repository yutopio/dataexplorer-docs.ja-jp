---
title: autocluster プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの autocluster プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a7caa8935176b2d4d52bf8955262509bb2069dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248046"
---
# <a name="autocluster-plugin"></a>autocluster プラグイン

```kusto
T | evaluate autocluster()
```

`autocluster` データ内の不連続属性 (ディメンション) の一般的なパターンを検索します。 次に、元のクエリの結果を、100か10万行でも、少数のパターンに減らします。 このプラグインは、エラー (例外やクラッシュなど) を分析するために開発されましたが、フィルター処理されたデータセットで動作する可能性があります。

> [!NOTE]
> `autocluster` は、主に、次のホワイトペーパーの Seed-Expand アルゴリズムに基づいています。 [個別の属性を使用したテレメトリデータマイニングのアルゴリズム](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)です。 


## <a name="syntax"></a>構文

`T | evaluate autocluster(`*引数*`)`

## <a name="returns"></a>戻り値

この `autocluster` プラグインは、(通常は小さい) パターンのセットを返します。 このパターンでは、複数の不連続属性で共通の値を共有して、データの一部をキャプチャします。 結果内の各パターンは、行によって表されます。

最初の列はセグメント ID です。 次の 2 つの列は、元のクエリからパターンによってキャプチャされた行の数と割合です。 残りの列は、元のクエリからの列です。 値は、列の特定の値か、または (既定では null である) ワイルドカード値によって変数値に意味があります。

パターンは区別されず、重複する可能性があり、通常は元のすべての行に対応していません。 一部の行は、どのパターンにも当てはまらない場合があります。

> [!TIP]
> 入力パイプで [where](./whereoperator.md) および [project](./projectoperator.md) を使用して、関心のあるものだけにデータを減らします。
>
> 関心がある行が見つかったら、 `where` フィルターに特定の値を追加して、より詳しく調べることができます。

## <a name="arguments"></a>引数 

> [!NOTE] 
> すべての引数は省略可能です。

`T | evaluate autocluster(`[*Sizeweight*、 *WeightColumn*、 *numseeds*、 *customwildcard*、 *customwildcard*、...]`)`

引数はすべて省略できますが、上記と同じ順序で指定する必要があります。 既定値を使用する必要があることを示すには、文字列のチルダの値 ' ~ ' を入力します (テーブルの "Example" 列を参照してください)。

|引数        | Type、range、default              |説明                | 例                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| SizeWeight     | 0 < *double* < 1 [既定値: 0.5]   | 汎用 (高カバレッジ) と情報提供 (多くの共有) 値の間のバランスを制御できます。 値を大きくすると、通常はパターンの数が減り、各パターンはより大きなカバレッジをカバーする傾向があります。 値を小さくすると、通常はより多くのより具体的なパターンが生成され、より多くの共有値が表示されます。 内部的な数式は、正規化された汎用スコアと、重みを持つ情報スコアとの間の加重幾何平均です。 `SizeWeight``1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|WeightColumn    | *column_name*                     | 入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。 引数には、数値列の名前 (int、long、real など) を指定する必要があります。 weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [既定値:25]              | シード数によって、アルゴリズムの最初のローカル検索ポイント数が決まります。 場合によっては、データの構造によっては、シードの数を増やすと、結果の数 (または品質) が、拡張された検索領域を超えるクエリのトレードオフによって増加します。 この値の結果は両方向に低下します。したがって、この値を5未満に減らすと、パフォーマンスがわずかに向上します。 50を超える場合、追加のパターンが生成されることはほとんどありません。                                         | `T | evaluate autocluster('~', '~', 15)`       |
| CustomWildcard  | *"any_value_per_type"*           | 結果テーブル内の特定の型のワイルドカード値を設定します。 現在のパターンには、この列に対する制限がないことを示します。 既定値は null です。これは、文字列の既定値が空の文字列であるためです。 既定値がデータ内の適切な値の場合は、別のワイルドカード値 (など) を使用する必要があり `*` ます。                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>例

### <a name="using-autocluster"></a>Autocluster の使用

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

### <a name="using-custom-wildcards"></a>カスタムワイルドカードの使用

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

## <a name="see-also"></a>関連項目

* [basket](./basketplugin.md)
* [落とし](./reduceoperator.md)
