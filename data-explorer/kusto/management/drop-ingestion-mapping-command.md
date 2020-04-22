---
title: .drop インジェスティマッピング - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .drop インジェスティ マッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c1434234033acc73de35289c6bc0a90af727babb
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744770"
---
# <a name="drop-ingestion-mapping"></a>.ate インジェスト マッピング

データベースから取り込みマッピングを削除します。
 
`.drop``table`*テーブル名*`ingestion`*マッピングKind* `mapping` *マッピング名*   

**例** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion JSON mappings "Mapping1" 
```
