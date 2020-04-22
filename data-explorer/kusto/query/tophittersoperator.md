---
title: トップヒッターオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのトップ ヒッター オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7105bd4f9818deab1f0fa5dbe25dbb2d5b1b4afc
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663121"
---
# <a name="top-hitters-operator"></a>top-hitters 演算子

最初の*N*の結果の近似値を返します (入力の分布が歪んでいるものと仮定します)。

```kusto
T | top-hitters 25 of Page by Views 
```

**構文**

*T* `| top-hitters` *の数の行*`of`*sort_key*`[``by`*式*`]`

**引数**

* *行数*: 返す*T*の行数。 任意の数値式を指定できます。
* *sort_key*: 行を並べ替える列の名前。
* *式*: (オプション)トップヒッターの推定に使用される式。 
    * *式*: トップヒッターは *、sum()* の最大値に近似した*NumberOfRows 行*を返します。 式は、列、または数値に評価される他の式です。 
    *  *式*が指定されていない場合、トップヒッターアルゴリズムは*ソートキー*の出現をカウントします。  

**メモ**

`top-hitters`は近似アルゴリズムであり、大きなデータで実行する場合に使用する必要があります。 トップヒッターの近似は[、カウントミンスケッチ](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)アルゴリズムに基づいています。  

**例**

## <a name="getting-top-hitters-most-frequent-items"></a>トップヒッターを取得する(最も頻繁なアイテム) 

次の例は、ウィキペディアのほとんどのページを含む上位 5 言語を検索する方法を示しています (2016 年 4 月以降にアクセス)。 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|Language|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

## <a name="getting-top-hitters-based-on-column-value-"></a>トップヒッターを取得する(列の値に基づく) ***

次の例は、2016年のウィキペディアで最も閲覧された英語のページを見つける方法を示しています。 クエリでは、ページの人気度 (ビュー数) を計算するために 'Views' (整数) を使用します。 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|ページ|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|Java_(programming_language)|16489491|
|United_States|13928841|
|ウィキペディア|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_(2015_film)|10714263|
|Star_Wars:_The_Force_Awakens|9770653|
|ポータル:Current_events|9578000|