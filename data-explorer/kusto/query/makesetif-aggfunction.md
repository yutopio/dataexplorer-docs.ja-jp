---
title: make_set_if () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_set_if () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 262676e2ee40b619c4984a23c23818c493aad47a
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103044"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if () (集計関数)

`dynamic`*述語*がと評価*される*グループ内の個別の値のセットの (JSON) 配列を返し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``make_set_if(` *Expr*、*述語*[ `,` *MaxSize*]`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。
* *述語*: `true` 結果に追加される *Expr* をに評価する必要がある述語。
* *MaxSize* は、返される要素の最大数に対する整数制限 (省略可能) です (既定値は *1048576*)。 MaxSize 値は1048576を超えることはできません。

## <a name="returns"></a>戻り値

`dynamic`*述語*がと評価*される*グループ内の個別の値のセットの (JSON) 配列を返し `true` ます。
配列の並べ替え順序が定義されていません。

> [!TIP]
> 個別の値のみをカウントするには、 [dcountif ()](dcountif-aggfunction.md)を使用します。

## <a name="see-also"></a>関連項目

[`make_set`](./makeset-aggfunction.md) 述語式を使用せずに同じを実行する関数。

## <a name="example"></a>例

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["ジョージ", "Ringo"]|