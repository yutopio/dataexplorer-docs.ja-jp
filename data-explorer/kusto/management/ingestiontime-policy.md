---
title: インジェスティションタイム ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのインジェスティションタイム ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c42da570b961595be1fbcae352fe121d8b6f59ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520895"
---
# <a name="ingestiontime-policy"></a>IngestionTime ポリシー

インジェストタイム ポリシーは、テーブルに対するオプションのポリシー セットです (既定では有効になっています)。
レコードをテーブルに取り込むおおよその時間を提供します。

取り込み時間の値は、関数を使用`ingestion_time()`してクエリ時にアクセスできます。

```kusto
T | extend ingestionTime = ingestion_time()
```

ポリシーを有効/無効にするには::
```kusto
.alter table table_name policy ingestiontime true
```

複数のテーブルのポリシーを有効/無効にするには、次の手順を実行します。
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

ポリシーを表示するには::
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

ポリシーを削除するには(無効にするのと同じ)
```kusto
.delete table table_name policy ingestiontime  
```