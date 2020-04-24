---
title: percentrank_tdigest() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの percentrank_tdigest() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: fe356ddb2e6301bbb283d2e6aa59b5c98f8bf3fe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511188"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

ランクがセットのサイズのパーセントで表されるセット内の値のおおよそのランクを計算します。 この関数は、パーセンタイルの逆関数として表示できます。

**構文**

`percentrank_tdigest(`*Tダイジェスト*`,`*エクスプ*`)`

**引数**

* *TDigest*:[消化器によって](tdigest-aggfunction.md)生成された式[tdigest_merge()](tdigest-merge-aggfunction.md).
* *Expr*: パーセントランキング計算に使用する値を表す式。

**戻り値**

データセット内の値のランクの割合。

**ヒント**

1) 2 番目のパラメーターの型と、消化器内の要素の型は同じである必要があります。

2) 最初のパラメータは[、tdigest()](tdigest-aggfunction.md)または[tdigest_merge()](tdigest-merge-aggfunction.md)によって生成された TDigest である必要があります。

**使用例**

4490$の値を持つ損害特性のpercentrank_tdigest()を得ることは〜85%です:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


損害特性の上にパーセンタイル85を使用すると、4490$を与える必要があります:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|