---
title: . alter table docstring-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの alter table docstring について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 790cd9805be6dd4440ef2eb51c504044dc069b3c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617784"
---
# <a name="alter-table-docstring"></a>.alter テーブル ドキュメント文字列

既存のテーブルの DocString 値を変更します。

`.alter``table` *TableName* TableName `docstring` *ドキュメント*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * テーブルの変更は、テーブルを最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)にも許可されます。
> * テーブルが存在しない場合は、エラーが返されます。 新しいテーブルを作成するには、「」を参照してください[。テーブルの作成](create-table-command.md)

**例** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```