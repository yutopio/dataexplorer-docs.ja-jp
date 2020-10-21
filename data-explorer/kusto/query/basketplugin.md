---
title: バスケットプラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのバスケットプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: 9436254d72f364edc1f6a758ce2325e272367293
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252674"
---
# <a name="basket-plugin"></a>basket プラグイン

```kusto
T | evaluate basket()
```

バスケットは、データ内の不連続属性 (ディメンション) のパターンをすべて検索します。 次に、元のクエリで頻度のしきい値に達した頻繁なパターンを返します。 バスケットはデータ内のすべての頻繁なパターンを検索することが保証されていますが、多項式のランタイムがあるとは限りません。 クエリの実行時間は行数に比例していますが、列数 (ディメンション) に指数関数を使用する場合もあります。 basket は、元はバスケット分析データ マイニング向けに開発された Apriori アルゴリズムに基づいています。

## <a name="syntax"></a>構文

`T | evaluate basket(`*引数*`)`

## <a name="returns"></a>戻り値

バスケットでは、行の比率のしきい値を超えた頻繁なパターンがすべて返されます。 既定のしきい値は0.05 です。 各パターンは、結果内の行によって表されます。

最初の列はセグメント ID です。 次の2つの列は、パターンによってキャプチャされた、元のクエリからの*行の**数*と割合を示します。 残りの列は、元のクエリからの列です。
値は、列の特定の値か、既定では null である、変数値を意味するワイルドカード値です。

**引数 (すべて省略可能)**

`T | evaluate basket(`[*Threshold*、 *WeightColumn*、 *maxdimensions*、 *customwildcard*、 *customwildcard*、...]`)`

引数はすべて省略できますが、上記と同じ順序で指定する必要があります。 既定値を使用する必要があることを示すには、"チルダの値-' ~ ' という文字列を使用します。 以下の例を参照してください。

使用可能な引数:

* しきい値-0.015 < *double* < 1 [既定値: 0.05]

    頻繁に見なされる行の最小比率を設定します。 比率が低いパターンは返されません。
    
    例: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    入力の各行を、指定された重みに従って見なします。 既定では、各行の重みは ' 1 ' になります。 引数には、int、long、real など、数値型の列の名前を指定する必要があります。 Weight 列の一般的な用途は、各行に既に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。

    例: `T | evaluate basket('~', sample_Count)`

* MaxDimensions-1 < *int* [既定値: 5]

    クエリランタイムを最小化するために、既定で制限されているバスケットごとの非相関ディメンションの最大数を設定します。

    例: `T | evaluate basket('~', '~', 3)`

* CustomWildcard- *"any_value_per_type"*

    現在のパターンにこの列に対する制限がないことを示す特定の種類のワイルドカード値を結果テーブルに設定します。
    既定値は Null です。 文字列の既定値は空の文字列です。 既定値がデータ内で適切な値である場合は、などの別のワイルドカード値を使用する必要があり `*` ます。

    次に例を示します。

     `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|セグメント ID|Count|Percent|State|EventType|損害|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|||NO|0
|1|2278|38.7||ひょう|NO|0
|2|5675|96.4||||0
|3|2371|40.3||ひょう||0
|4|1279|21.7||雷雨風||0
|5|2468|41.9||ひょう||
|6|1310|22.3|||YES|
|7|1291|21.9||雷雨風||

**カスタム ワイルドカードを使用した例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|セグメント ID|Count|Percent|State|EventType|損害|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|\*|\*|NO|0
|1|2278|38.7|\*|ひょう|NO|0
|2|5675|96.4|\*|\*|\*|0
|3|2371|40.3|\*|ひょう|\*|0
|4|1279|21.7|\*|雷雨風|\*|0
|5|2468|41.9|\*|ひょう|\*|-1
|6|1310|22.3|\*|\*|YES|-1
|7|1291|21.9|\*|雷雨風|\*|-1
