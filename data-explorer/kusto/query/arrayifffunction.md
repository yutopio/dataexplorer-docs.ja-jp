---
title: array_iif ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの array_iif () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 1844d87dffb5ac0046c3f62600b8c4914dc93a89
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349586"
---
# <a name="array_iif"></a>array_iif()

動的配列での要素ごとの iif 関数。

別の別名: array_iff ()。

## <a name="syntax"></a>構文

`array_iif(`*Conditionarray*、 *iftrue*、 *IfFalse*]`)`

## <a name="arguments"></a>引数

* *Conditionarray*:*ブール*値または数値の入力配列。動的配列である必要があります。
* *Iftrue*: 値またはプリミティブ値の入力配列。 *conditionarray*の対応する値が*true*の場合の結果値。
* *ifFalse*: 値またはプリミティブ値の入力配列。 *conditionarray*の対応する値が*false*の場合の結果値。

**ノート**

* 結果の長さは*Conditionarray*の長さです。
* 数値条件値は、 *condition* ! = *0*として扱われます。
* 数値以外の条件値または null 条件値は、結果の対応するインデックスに null を保持します。
* 不足値 (より短い長さの配列) は null として扱われます。

## <a name="returns"></a>戻り値

Condition 配列の対応する値に従って、 *Iftrue*または*IfFalse* [array] の値から取得した値の動的配列。

## <a name="example"></a>例

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[true、false、true]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
