---
title: bag_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bag_merge () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 66a05cdc03b155b8fceace0af8d86807a10d0da4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349365"
---
# <a name="bag_merge"></a>bag_merge()

プロパティ `dynamic` バッグを、 `dynamic` すべてのプロパティがマージされたプロパティバッグにマージします。

## <a name="syntax"></a>構文

`bag_merge(`*bag1* `, `*bag2* `[` 、` *bag3*, ...])`

## <a name="arguments"></a>引数

* *bag1...bagN*: 入力 `dynamic` プロパティ-バッグ。 関数は、2 ~ 64 の引数を受け取ることができます。

## <a name="returns"></a>戻り値

`dynamic`プロパティバッグを返します。 すべての入力プロパティバッグオブジェクトを結合した結果。 複数の入力オブジェクトにキーが表示される場合は、任意の値 (このキーで使用可能な値のうちのいずれか) が選択されます。

## <a name="example"></a>例

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
