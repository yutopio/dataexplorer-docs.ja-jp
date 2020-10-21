---
title: percentrank_tdigest ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの percentrank_tdigest () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: dd784d8968b45a735bd2df840a09c349e2fdcbd2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249694"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

セット内の値のおおよその順位を計算します。順位は、セットのサイズに対する比率として表されます。
この関数は、パーセンタイルの逆として表示できます。

## <a name="syntax"></a>構文

`percentrank_tdigest(`*Tdigest* `,`*Expr*`)`

## <a name="arguments"></a>引数

* *Tdigest*: [tdigest ()](tdigest-aggfunction.md) または [tdigest_merge ()](tdigest-merge-aggfunction.md)によって生成された式。
* *Expr*: 割合の順位計算に使用される値を表す式。

## <a name="returns"></a>戻り値

データセット内の値の割合のランク。

**ヒント**

1) 2番目のパラメーターの型と、内の要素の型は同じである `tdigest` 必要があります。

2) 最初のパラメーターは、 [tdigest ()](tdigest-aggfunction.md)または[tdigest_merge ()](tdigest-merge-aggfunction.md)によって生成された tdigest である必要があります

## <a name="examples"></a>例

Percentrank_tdigest () を取得します。このプロパティの値は $4490 になります85が、次のようになります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|列 1|
|---|
|85.0015237192293|


ダメージのプロパティで百分位85を使用すると、$4490 になります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
