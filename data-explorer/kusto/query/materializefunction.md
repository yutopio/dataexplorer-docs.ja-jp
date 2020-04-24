---
title: マテリアライズ() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの具体化() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: dfaed8cf972a517c86717999b3e5423c0ddd1fb0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512616"
---
# <a name="materialize"></a>materialize()

他のサブクエリが部分結果を参照できるように、クエリの実行中にサブクエリの結果をキャッシュできます。

 
**構文**

`materialize(`*expression*`)`

**引数**

* *式*: クエリの実行中に評価およびキャッシュされる表形式の式。

**ヒント**

* materialize は、join/union のオペランドに 1 回実行できる共通のサブクエリがある場合に使用します (下の例を参照)。

* また、join/union の分岐が必要な場合にも役立ちます。

* materialize を使用できるのは、let ステートメントでキャッシュ結果に名前を付けている場合のみです。

* マテリアライズには **、5 GB**のキャッシュサイズ制限があります。 
  この制限はクラスター ノードごとに、同時に実行されているすべてのクエリに対して相互に使用されます。
  クエリが使用`materialize()`し、キャッシュに追加のデータを保持できない場合、クエリは中止され、エラーが発生します。

**使用例**

ランダムな値のセットを生成し、どの程度の異なる値を持っている、すべてのこれらの値と上位 3 つの値を見つけることに興味があると仮定します。

これは[バッチ](batches.md)を使用して行い、具体化することができます:

 ```kusto
let randomSet = materialize(range x from 1 to 30000000 step 1
| project value = rand(10000000));
randomSet
| summarize dcount(value);
randomSet
| top 3 by value;
randomSet
| summarize sum(value)

```