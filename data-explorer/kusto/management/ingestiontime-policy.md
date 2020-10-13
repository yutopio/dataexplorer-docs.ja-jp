---
title: Kusto IngestionTime ポリシー管理-Azure データエクスプローラー
description: Azure データエクスプローラーの IngestionTime policy コマンドについて理解を深めます。 インジェスト時間にアクセスして、このポリシーをオンまたはオフにする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e1671373547a4ed069d67bc9153248f23bc3b5e
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003101"
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