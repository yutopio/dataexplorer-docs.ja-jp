---
title: . alter table フォルダー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの alter table フォルダーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8ba9eb06e07dd513d20cdade87322d9ef5f17d24
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321643"
---
# <a name="alter-table-folder"></a>.alter テーブル フォルダー

既存のテーブルのフォルダー値を変更します。 

`.alter``table` *TableName* `folder` *フォルダー*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * テーブルを最初に作成した [データベースユーザー](../management/access-control/role-based-authorization.md) も、そのテーブルを編集できます。
> * テーブルが存在しない場合は、エラーが返されます。 新しいテーブルを作成する場合は、「」を参照してください。 [`.create table`](create-table-command.md)

**使用例** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```