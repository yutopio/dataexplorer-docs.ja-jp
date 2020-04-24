---
title: diffpatterns_textプラグイン - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーdiffpatterns_textプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58f059e2346dfb3f15bff295126a14a97f10f317
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516016"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_textプラグイン

文字列値の 2 つのデータ セットを比較し、2 つのデータ セット間の違いを特徴付けテキスト パターンを検索します。

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

は`diffpatterns_text`、2 つのセット内のデータの異なる部分をキャプチャするテキスト パターンのセットを返します (つまり、条件が条件である場合は行の大部分を`true`キャプチャするパターン、条件が条件の場合は`false`行の割合が低くなります)。 パターンは、テキスト列のトークンまたはワイルドカードを`*`表すトークンを使用して、連続したトークン (空白で区切られた) から構築されます。 各パターンは、結果内の行によって表されます。

**構文**

`T | evaluate diffpatterns_text(`テキスト列、ブール条件 [, 最小トークン, しきい値, 最大トークン]`)` 

**必須の引数**

* テキスト列 - *column_name*

    分析するテキスト列は、string 型である必要があります。
    
* ブール条件 -*ブール式*

    入力テーブルと比較する 2 つのレコード サブセットを生成する方法を定義します。 このアルゴリズムは、条件に従ってクエリを "True" と "False" の 2 つのデータセットに分割し、その間の (テキスト) の違いを分析します。 

**省略可能な引数**

その他の引数はすべて省略できますが、下記と同じ順序で指定する必要があります。 

* MinTokens - 0 < *int* < 200 [デフォルト: 1]

    結果パターンごとのワイルドカード以外のトークンの最小数を設定します。

* しきい値 - 0.015 <*ダブル*< 1 [デフォルト: 0.05]

    2 つのセット間の最小パターン(比)の差を設定します[(diffpatterns](diffpatternsplugin.md)を参照)。

* MaxTokens - 0 < *int* [デフォルト: 20]

    結果パターンごとのトークンの最大数 (先頭から) を設定し、下限を指定するとクエリの実行時間が減少します。

**戻り値**

diffpatterns_textの結果は、次の列を返します。

* Count_of_True: 条件が`true`.
* Count_of_False: 条件が`false`.
* Percent_of_True: 条件が の場合、行のパターンに一致する`true`行の割合です。
* Percent_of_False: 条件が の場合、行のパターンに一致する`false`行の割合です。
* パターン: テキスト文字列のトークンを含むテキスト パターン`*`と、ワイルドカードの場合は ' を含むテキスト パターン。 

> [!NOTE]
> パターンは必ずしも明確ではなく、データ セットの完全なカバレッジを提供しない場合があります。 パターンが重なっている可能性があり、一部の行はパターンに一致しない場合があります。

**例**

```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```
|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|パターン|
|---|---|---|---|---|
|11|0|6.29|0|*ウェイク*の北西にシフトする風*表面トラフは、重い湖効果降雪のダウンウィンドをもたらしました* からスーペリア湖|
|9|0|5.14|0|カナダの高圧は**地域*2006年2月以来最も寒い気温を生み出しました。 持続時間 * 凍結温度|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * * * * * 西テネシー州,|
|0|42|0|7.71|* * * * * * * 引き起こされた * * * * * * * * * コロラド州西部を横切る。 *|
|0|45|0|8.26|* * 通常以下 *|
|0|110|0|20.18|通常より低い*|

