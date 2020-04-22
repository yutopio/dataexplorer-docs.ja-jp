---
title: array_iif() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでarray_iif() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: f99b9aa8a9d081a7f28cd2e5bb8750b15f2fcdac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663914"
---
# <a name="array_iif"></a>array_iif()

動的配列の要素に基づく iif 関数。

もう一つの別名:array_iff()

**構文**

`array_iif(`*条件配列*、 *True の場合* *、False の場合*]`)`

**引数**

* *条件配列*:*ブール値*または数値の入力配列は、動的配列である必要があります。
* *ifTrue*: 値またはプリミティブ値の入力配列 - 対応する*ConditionArray*の値が*true*の場合の結果値です。
* *ifFalse*: 値またはプリミティブ値の入力配列 - 対応する*ConditionArray*の値が*false*の場合の結果値です。

**メモ**

* 結果の長さは *、条件の長さ配列です*。
* 条件値は*条件*!= *0*として扱われます。
* 非数値/NULL 条件値は、結果の対応するインデックスに NULL を持ちます。
* 欠損値 (短い長さの配列) は null として扱われます。

**戻り値**

条件配列の対応する値に従って *、IfTrue*または*IfFalse* [配列] の値から取得された値の動的配列。

**例**

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[真、偽、真実]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
