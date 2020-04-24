---
title: バッチ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのバッチについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd695a1e7ffd9980de2750d38ad9538eeb877538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518022"
---
# <a name="batches"></a>バッチ

クエリには、セミコロン (`;`) 文字で区切られている限り、複数の表形式の式ステートメントを含めることができます。 次に、クエリは、表形式の式ステートメントによって生成された複数の表形式の結果を返し、クエリ テキスト内のステートメントの順序に従って順序付けされます。

たとえば、次のクエリは 2 つの表形式の結果を生成します。 ユーザー エージェント ツールは、それぞれの (`Count of events in Florida`と`Count of events in Guam`) に関連付けられた適切な名前を持つ結果を表示できます。

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

バッチは、ダッシュボードなど、複数のサブクエリで共有される共通の計算があるシナリオで特に便利です。 一般的な計算が複雑な場合は、次の方法で[、次](./materializefunction.md)の関数を使用してクエリを 1 回だけ実行するようにします。

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

メモ:
* [fork](forkoperator.md)演算子[`materialize`](materializefunction.md)を使用してバッチ処理を行う方が好きです。