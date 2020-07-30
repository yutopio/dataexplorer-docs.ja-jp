---
title: dcountif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dcountif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac07b51135c202f611ba28931eebf79ef5e996f1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348396"
---
# <a name="dcountif-aggregation-function"></a>dcountif () (集計関数)

*述語*がと評価される行の*Expr*の個別の値の推定値を返し `true` ます。 

* 集計のコンテキスト内でのみ使用できます[。](summarizeoperator.md)

[推定精度](dcount-aggfunction.md#estimation-accuracy)を確認します。

## <a name="syntax"></a>構文

集計の `dcountif(` *Expr*、*述語*、[ `,` *精度*]`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。
* *Predicate*: 行をフィルター処理するために使用される式。
* *Accuracy*を指定した場合は、速度と精度のバランスが制御されます。
    * `0` = 精度は最も低くなりますが、計算速度は最高になります。 1.6% エラー
    * `1`= 既定値。精度と計算時間のバランスを取るために使用します。約0.8% のエラー。
    * `2`= 正確で低速な計算。約0.4% のエラー。
    * `3`= 追加の正確で低速な計算。約0.28% のエラー。
    * `4`= 非常に正確で低速な計算。約0.2% のエラー。
    
## <a name="returns"></a>戻り値

グループ内で*述語*が評価される行の*Expr*の個別の値の推定値を返し `true` ます。 

## <a name="example"></a>例

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**ヒント: オフセットエラー**

`dcountif()`すべての行が成功した場合、または行がパスしていない場合、 `Predicate` 式