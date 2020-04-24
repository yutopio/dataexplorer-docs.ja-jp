---
title: トスカラ() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで toscalar() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 60fe8123760a9921bfa7abfacbbdffba6dba8d7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505901"
---
# <a name="toscalar"></a>toscalar()

評価された式のスカラー定数値を返します。 

この関数は、イベントの合計数を計算し、すべてのイベントの特定の割合を超えるグループのフィルタリングに使用するなど、段階的な計算を必要とするクエリに役立ちます。 

**構文**

`toscalar(`*式*`)`

**引数**

* *式*: スカラー変換の評価を受ける式  

**戻り値**

評価された式のスカラー定数値。
式の結果が表形式の場合、最初の列と最初の行が変換用に取られます。

> [!TIP]
> を使用する場合、クエリを読みやすくするために[let](letstatement.md) `toscalar()`ステートメントを使用できます。

**メモ**

`toscalar()`クエリの実行中に定数回数を計算できます。
つまり、(`toscalar()`行ごとのシナリオ) の行レベルで関数を適用することはできません。

**使用例**

次のクエリでは、 `Start``End`と`Step`をスカラー定数として評価し、評価に`range`使用します。 

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|ステップ|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|