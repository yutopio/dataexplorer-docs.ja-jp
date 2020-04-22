---
title: バスケット プラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのバスケット プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: 06fd1e33624bca6aee18a1ca969af18656c6b3e0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663878"
---
# <a name="basket-plugin"></a>バスケットのプラグイン

```kusto
T | evaluate basket()
```

basket は、データの個々の属性 (ディメンション) のすべての頻出パターンを検出し、元のクエリの頻度しきい値を超えたすべての頻出パターンを返します。 バスケットは、データ内のすべての頻繁なパターンを検索することが保証されていますが、多項式の実行時を持つことは保証されていませんが、クエリの実行時は行数に比例していますが、場合によっては列数 (ディメンション) の指数関数である場合があります。 basket は、元はバスケット分析データ マイニング向けに開発された Apriori アルゴリズムに基づいています。

**構文**

`T | evaluate basket(`*引数*`)`

**戻り値**

basket は、行の割合しきい値 (既定値 : 0.05) を超える頻出パターンをすべて返します。各パターンは、結果内の行によって表されます。

最初の列はセグメント Id です。次の 2 つの列は、パターンによってキャプチャされた元のクエリの行数と割合です。 残りの列は、元のクエリにあったもので、それらの値は列の具体的な値か、変数値を意味するワイルド カード (既定では null) です。

**引数 (すべて省略可能)**

`T | evaluate basket(`[*しきい値*,*重量列*,*最大寸法 ,**カスタムワイルドカード*,*カスタムワイルドカード*, .]`)`

引数はすべて省略できますが、上記と同じ順序で指定する必要があります。 既定値を使用することを示すには、文字列チルダ値 '~' を使用します (以下の例を参照)。

使用可能な引数:

* しきい値 - 0.015 <*ダブル*< 1 [デフォルト: 0.05]

    頻出と見なされる行の最小率を設定します (より少ない率のパターンは返されません)。
    
    例: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。 引数は数値列の名前にする必要があります (例 : int、long、real)。 weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
    
    例: `T | evaluate basket('~', sample_Count)`

* 最大寸法 - 1 < *int* [デフォルト: 5]

    バスケットごとの非相関ディメンションの最大数を設定します。既定では、クエリの実行時間を短くするために制限されています。

    例: `T | evaluate basket('~', '~', 3)`

* カスタムワイルドカード - *"any_value_per_type"*

    現在のパターンにこの列に対する制限がないことを示す特定の種類のワイルドカード値を結果テーブルに設定します。
    既定値は null です。文字列の場合、既定値は空の文字列です。 デフォルトがデータ内の有効な値である場合は、別のワイルドカード値を使用する必要があります (例`*`えば)。
    次の例をご覧ください。

    例: `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**例**

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
