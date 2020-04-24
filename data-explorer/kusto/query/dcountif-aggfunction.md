---
title: dcountif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの dcountif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1cc3c17474db835f381cac32d6107acb312c3d11
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516050"
---
# <a name="dcountif-aggregation-function"></a>dcountif() (集計関数)

*述語*が 評価される行の*Expr*の個別値の数の見積もり`true`を返します。 

* [集計](summarizeoperator.md)内の集計のコンテキストでのみ使用できます。

[推定精度](dcount-aggfunction.md#estimation-accuracy)についてお読みください。

**構文**

`dcountif(`*要約 Expr*,`,`*述語*, [*精度*]`)`

**引数**

* *Expr*: 集計の計算に使用する式。
* *述語*: 行のフィルタ処理に使用する式。
* *Accuracy*を指定した場合は、速度と精度のバランスが制御されます。
    * `0` = 精度は最も低くなりますが、計算速度は最高になります。 1.6% エラー
    * `1`= 精度と計算時間のバランスをとるデフォルト値。約0.8%の誤差。
    * `2`= 正確で遅い計算;約0.4%の誤差。
    * `3`= 余分な正確で遅い計算;約0.28%の誤差。
    * `4`= 超正確で最も遅い計算;約0.2%の誤差。
    
**戻り値**

グループ内で*Predicate*が評価する対象の行の*Expr*の個別値`true`の数の見積もりを返します。 

**例**

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**ヒント: オフセット エラー**

`dcountif()`すべての行が通過するエッジの場合、またはどの行も渡されない場合、1 回限りのエラーが発生する`Predicate`可能性があります。