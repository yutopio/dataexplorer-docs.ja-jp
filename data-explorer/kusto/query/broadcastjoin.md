---
title: ブロードキャスト参加 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのブロードキャスト参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72c89bf2160f8f5b735fd8c93f9519feae9114d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517308"
---
# <a name="broadcast-join"></a>ブロードキャスト結合

現在、通常の結合は単一のクラスタ ノードで実行されます。
ブロードキャスト結合は、クラスタノード上で配布するjoinの実行戦略であり、結合の左側が小さい(最大100Kレコード)場合に便利です。

結合の左側が小さなデータセットの場合は、次の構文 (hint.strategy = ブロードキャスト) を使用して、ブロードキャスト モードで join を実行できます。

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

パフォーマンスの向上は、結合の後に、 などの`summarize`他の演算子が続くシナリオで、より顕著になります。 たとえば、次のクエリを使用します。

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```