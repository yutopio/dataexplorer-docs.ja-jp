---
title: バッチ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのバッチについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5a38d53fb9b28fc7da0ddf71132e3a047e8188e
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550335"
---
# <a name="batches"></a>バッチ

クエリには、セミコロン () 文字で区切られている限り、複数の表形式式ステートメントを含めることができ `;` ます。 このクエリでは、複数の表形式の結果が返されます。 結果は、テーブル式ステートメントによって生成され、クエリテキスト内のステートメントの順序に従って並べ替えられます。

たとえば、次のクエリでは、2つの表形式の結果が生成されます。 ユーザーエージェントツールは、それぞれに関連付けられた適切な名前を使用して結果を表示でき `Count of events in Florida` `Count of events in Guam` ます (それぞれと)。

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Batch は、複数のサブクエリ (ダッシュボードなど) で共通の計算が共有されるシナリオに役立ちます。 一般的な計算が複雑な場合は、[具体化 () 関数](./materializefunction.md)を使用してクエリを作成し、1回だけ実行されるようにします。

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

メモ:
* [`materialize`](materializefunction.md)[フォーク演算子](forkoperator.md)を使用してバッチ処理を優先します。
