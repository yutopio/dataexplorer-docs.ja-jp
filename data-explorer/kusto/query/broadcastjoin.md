---
title: Broadcast Join-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのブロードキャスト結合について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7987f44437d673b90954b674e63b70ac35e2432c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128684"
---
# <a name="broadcast-join"></a>ブロードキャスト結合

現在、1つのクラスターノードで正規の結合が実行されています。
Broadcast join は join の実行戦略であり、クラスターノードに分散されます。 この方法は、結合の左側が小さい場合 (最大 100 K レコード) に役立ちます。 この場合、broadcast join は通常の結合よりもパフォーマンスが高くなります。

結合の左側が小規模なデータセットである場合は、次の構文を使用してブロードキャストモードで結合を実行できます (ヒント. 戦略 = ブロードキャスト)。

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

結合の後になどの他の演算子が続くシナリオでは、パフォーマンスの向上が顕著になり `summarize` ます。 このクエリの例を次に示します。

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```