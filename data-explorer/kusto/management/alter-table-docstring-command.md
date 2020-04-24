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
ms.openlocfilehash: 5b3fa21b8b9012fe23a82afea77b1a3e24ae3c91
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108475"
---
# <a name="alter-table-docstring"></a>.alter テーブル ドキュメント文字列

既存のテーブルの DocString 値を変更します。

`.alter``table` *TableName* TableName `docstring` *ドキュメント*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * テーブルの変更は、テーブルを最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)にも許可されます。
> * テーブルが存在しない場合は、エラーが返されます。 新しいテーブルを作成するには、「」を参照してください[。テーブルの作成](create-table-command.md)

**例** 

```
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```