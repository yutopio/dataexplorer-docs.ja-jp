---
title: bag_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bag_merge () について説明します。
services: data-explorer
author: orspod
ms.author: orspod
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 0a23f6ece8be3ba451c1f61a90eb65452b68f9ce
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265065"
---
# <a name="bag_merge"></a>bag_merge()

プロパティ `dynamic` バッグを、 `dynamic` すべてのプロパティがマージされたプロパティバッグにマージします。

**構文**

`bag_merge(`*bag1* `, `*bag2* `[` 、` *bag3*, ...])`

**引数**

* *bag1...bagN*: 入力 `dynamic` プロパティ-バッグ。 関数は、2 ~ 64 の引数を受け取ることができます。

**戻り値**

`dynamic`プロパティバッグを返します。 すべての入力プロパティバッグオブジェクトを結合した結果。 複数の入力オブジェクトにキーが表示される場合は、任意の値 (このキーで使用可能な値のうちのいずれか) が選択されます。

**例**

式:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result = bag_merge(
   dynamic({'A1':12, 'B1':2, 'C1':3}),
   dynamic({'A2':81, 'B2':82, 'A1':1}))
```

|結果|
|---|
|{<br>  "A1":12,<br>  "B1": 2、<br>  "C1": 3、<br>  "A2":81、<br>  "B2":82<br>}|
