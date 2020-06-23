---
title: toscalar ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの toscalar () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15d9056ec21eb6f25ccbc985d659f310d670f02d
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133415"
---
# <a name="toscalar"></a>toscalar()

評価された式のスカラー定数値を返します。 

この関数は、ステージング計算を必要とするクエリに役立ちます。 たとえば、イベントの合計数を計算し、その結果を使用して、すべてのイベントの特定の割合を超えるグループをフィルター処理します。

**構文**

`toscalar(`*条件*`)`

**引数**

* *式*: スカラー変換のために評価される式。

**戻り値**

評価された式のスカラー定数値。
結果が表形式の場合は、最初の列と最初の行が変換に使用されます。

> [!TIP]
> を使用すると、クエリを読みやすくするために[let ステートメント](letstatement.md)を使用でき `toscalar()` ます。

**ノート**

`toscalar()`は、クエリの実行中に一定の回数だけ計算できます。
`toscalar()`関数は行レベルでは適用できません (行ごとのシナリオ)。

**使用例**

`Start`、 `End` 、およびを `Step` スカラー定数として評価し、結果を評価に使用し `range` ます。

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
