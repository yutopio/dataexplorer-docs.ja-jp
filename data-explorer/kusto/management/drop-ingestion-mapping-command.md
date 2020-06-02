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
ms.openlocfilehash: 7454bd86a6ca2a835dc0515a9c8901a444259f12
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294527"
---
# <a name="drop-ingestion-mapping"></a>.ate インジェスト マッピング

データベースからインジェストマッピングを削除します。
 
`.drop``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

**例** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
