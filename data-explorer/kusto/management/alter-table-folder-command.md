---
title: .alter テーブル フォルダ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの .alter テーブル フォルダーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: a1d368d50f0e42acdbc25348b8025fbe8b530536
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522306"
---
# <a name="alter-table-folder"></a>.alter テーブル フォルダー

既存のテーブルの Folder 値を変更します。 

`.alter``table`*テーブル名*`folder`*フォルダ*

> [!NOTE]
> * [データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要
> * テーブルを最初に作成した[データベース ユーザー](../management/access-control/role-based-authorization.md)も、テーブルを編集できます。
> * テーブルが存在しない場合は、エラーが返されます。 新しいテーブルを作成する場合は、[テーブルの作成](create-table-command.md)を参照してください。

**使用例** 

```
.alter table MyTable folder "Updated folder"
```

```
.alter table MyTable folder @"First Level\Second Level"
```