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
ms.openlocfilehash: f5ffc7ae648a9254564af0705cda84f3c79da99b
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966943"
---
# <a name="ingestiontime-policy-command"></a>ingestiontime ポリシー コマンド

IngestionTime policy は、テーブルに対して設定されたオプションのポリシーです (既定では有効になっています)。
テーブルへのレコードの取り込みのおおよその時間を提供します。

インジェスト time 値には、関数を使用したクエリ時にアクセスでき `ingestion_time()` ます。

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