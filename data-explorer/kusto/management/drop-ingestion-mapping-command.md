---
title: 。インジェストマッピングを削除します-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのインジェストのマッピングの削除について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 69ab4849d67d7cc7507bab075b0111fbd8ec95e7
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780611"
---
# <a name="drop-ingestion-mapping"></a>.ate インジェスト マッピング

データベースからインジェストマッピングを削除します。
 
`.drop``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

**例** 

```kusto
.drop table MyTable ingestion csv mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
