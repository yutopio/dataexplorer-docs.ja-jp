---
title: 。テーブルデータのクリア-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのコマンドについて説明し `.clear table data` ます。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
ms.date: 10/01/2020
ms.openlocfilehash: 513544b8fb373f7242bd36b250790801b39061b2
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615079"
---
# <a name="clear-table-data"></a>。テーブルデータをクリアします

ストリーミングインジェストデータを含む既存のテーブルのデータを消去します。

`.clear``table` *TableName*`data` 

> [!NOTE]
> * [Table admin 権限](../management/access-control/role-based-authorization.md)が必要です

**例** 

```kusto
.clear table LyricsAsTable data 
```
 
