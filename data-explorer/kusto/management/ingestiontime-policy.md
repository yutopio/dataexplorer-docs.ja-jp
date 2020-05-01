---
title: Kusto IngestionTime ポリシー管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの IngestionTime ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 907c6ddf84d772f800fce45d3c1245bbd11b0c85
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616458"
---
# <a name="ingestiontime-policy"></a>IngestionTime ポリシー

IngestionTime policy は、テーブルに対して設定されたオプションのポリシーです (既定では有効になっています)。
テーブルへのレコードの取り込みのおおよその時間を提供します。

インジェスト time 値には、関数を使用し`ingestion_time()`たクエリ時にアクセスできます。

```kusto
T | extend ingestionTime = ingestion_time()
```

ポリシーを有効または無効にするには:
```kusto
.alter table table_name policy ingestiontime true
```

複数のテーブルのポリシーを有効または無効にするには、次のようにします。
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

ポリシーを表示するには:
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

ポリシーを削除するには (無効にするのと同じ):
```kusto
.delete table table_name policy ingestiontime  
```