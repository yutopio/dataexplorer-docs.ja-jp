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
ms.openlocfilehash: f03a2927d3f0a4fe7ee1719594f2d1f19e231d0f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618362"
---
# <a name="alter-table-folder"></a>.alter テーブル フォルダー

既存のテーブルのフォルダー値を変更します。 

`.alter``table` *TableName* TableName `folder` *フォルダー*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * テーブルを最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)も、そのテーブルを編集できます。
> * テーブルが存在しない場合は、エラーが返されます。 新しいテーブルを作成する方法については、「」を参照してください[。テーブルの作成](create-table-command.md)

**使用例** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```