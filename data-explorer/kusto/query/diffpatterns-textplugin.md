---
title: diffpatterns_text プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの diffpatterns_text プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0a71f9db9062d83f55ebf9db1efabb6d86f9786
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803286"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text プラグイン

文字列値の2つのデータセットを比較し、2つのデータセットの違いを特徴付けるテキストパターンを検索します。

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

は、 `diffpatterns_text` 2 つのセット内のデータのさまざまな部分をキャプチャするテキストパターンのセットを返します (つまり、条件がのときに行の大きな割合をキャプチャし、 `true` 条件がの場合は行の割合を小さくします `false` )。 パターンは、(空白で区切られた) 連続するトークンから構築され、テキスト列のトークン、または `*` ワイルドカードを表すから作成されます。 各パターンは、結果内の行によって表されます。

## <a name="syntax"></a>構文

`T | evaluate diffpatterns_text(`TextColumn、BooleanCondition [、MinTokens、Threshold、MaxTokens]`)` 

## <a name="arguments"></a>引数

### <a name="required-arguments"></a>必須の引数

* TextColumn- *column_name*

    分析するテキスト列は文字列型である必要があります。
    
* BooleanCondition-*ブール式*

    入力テーブルと比較する2つのレコードのサブセットを生成する方法を定義します。 このアルゴリズムでは、クエリが条件に従って "True" と "False" の2つのデータセットに分割され、その間の (テキスト) の相違点が分析されます。 

### <a name="optional-arguments"></a>省略可能な引数。

その他の引数はすべて省略できますが、下記と同じ順序で指定する必要があります。 

* MinTokens-0 < *int* < 200 [既定値: 1]

    結果パターンごとに、ワイルドカード以外のトークンの最小数を設定します。

* しきい値-0.015 < *double* < 1 [既定値: 0.05]

    2つのセット間の最小パターン (比率) の差を設定します (「[パターン](diffpatternsplugin.md)の違い」を参照してください)。

* MaxTokens-0 < *int* [既定値:20]

    結果パターンごとのトークンの最大数 (先頭から) を設定します。下限を指定すると、クエリの実行時間が短縮されます。

## <a name="returns"></a>戻り値

Diffpatterns_text の結果は、次の列を返します。

* Count_of_True: 条件がの場合に、パターンに一致する行の数 `true` 。
* Count_of_False: 条件がの場合に、パターンに一致する行の数 `false` 。
* Percent_of_True: 条件がの場合に、行のパターンに一致する行の割合 `true` 。
* Percent_of_False: 条件がの場合に、行のパターンに一致する行の割合 `false` 。
* Pattern: テキスト文字列からのトークンを含むテキストパターンと、 `*` ワイルドカードの場合は ' '。 

> [!NOTE]
> パターンは必ずしも明確ではなく、データセット全体を網羅しているとは限りません。 パターンが重複している可能性があり、一部の行がどのパターンとも一致しない可能性があります。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```

|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|Pattern|
|---|---|---|---|---|
|11|0|6.29|0|* Wake * で北西に移動する上空では、大きなレイク効果を snowfall ダウンウィンド * Lake trough に移行|
|9|0|5.14|0|カナダの高負荷が決済された * * 地域 * 2 月 * 2006 以降、併置された温度が生成されました。 期間 * 固定温度|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * Tennessee 西部|
|0|42|0|7.71|* * * * * *、西洋コロラド州間で * * * * * * * *。 *|
|0|45|0|8.26|* * 標準 *|
|0|110|0|20.18|標準以下 *|
