---
title: . alter table docstring-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのコマンドについて説明し `.alter table docstring` ます。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 65b71ab15763506683c461f04975d22d396f6405
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512539"
---
# <a name="alter-table-docstring"></a>.alter テーブル ドキュメント文字列

既存のテーブルの DocString 値を変更します。

`.alter``table` *TableName* `docstring` *ドキュメント*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * テーブルを最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)は、そのテーブルを変更することが許可されています
> * テーブルが存在しない場合は、エラーが返されます。 新しいテーブルを作成するには、「」を参照してください[。テーブルの作成](create-table-command.md)

**例** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
