---
title: top-hitters 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの top-hitters 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: babb4e023d29c7894661e3acf2c0a09e753011c2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340821"
---
# <a name="top-hitters-operator"></a>top-hitters 演算子

最初の*N 個*の結果の近似値を返します (入力の非対称分布を想定)。

```kusto
T | top-hitters 25 of Page by Views 
```

## <a name="syntax"></a>構文

*T* `| top-hitters` *numberofrows* `of` *sort_key* `[` `by` *式*`]`

## <a name="arguments"></a>引数

* *Numberofrows*: 返す*T*の行数。 任意の数値式を指定できます。
* *sort_key*: 行の並べ替えに使用する列の名前。
* *式*: (省略可能) top-hitters の推定に使用される式。 
    * *式*: top-hitters は、近似値の最大値 (*式*) を持つ*numberofrows*行を返します。 式には、列、または数値に評価されるその他の式を指定できます。 
    *  *Expression*が指定されていない場合は、top-hitters アルゴリズムによって*並べ替えキー*の出現回数がカウントされます。  

**ノート**

`top-hitters`は近似値のアルゴリズムであり、大規模なデータを使用して実行する場合に使用する必要があります。 Top-hitters の概数は、 [Count-Min スケッチ](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)アルゴリズムに基づいています。  

## <a name="example"></a>例

## <a name="getting-top-hitters-most-frequent-items"></a>上位の top-hitters を取得する (最も頻繁に発生する項目) 

次の例は、Wikipedia のほとんどのページで上位5つの言語を検索する方法を示しています (2016 年4月の後にアクセス)。 

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

## <a name="getting-top-hitters-based-on-column-value-"></a>(列の値に基づいて) top top-hitters を取得する * * *

次の例では、2016年の Wikipedia の最もよく表示される英語のページを検索する方法を示します。 このクエリでは、' Views ' (整数数値) を使用してページの人気度 (ビュー数) を計算します。 

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
|Java_ (programming_language)|16489491|
|United_States|13928841|
|Wikipedia|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film)|10714263|
|Star_Wars: _The_Force_Awakens|9770653|
|ポータル: Current_events|9578000|