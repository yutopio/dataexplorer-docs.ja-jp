---
title: 制限付きビューアクセス ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの制限されたビュー アクセス ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fcf37d30bfe3ab0f9c4b5d4a720e6a5ba4ffe34
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520453"
---
# <a name="restrictedviewaccess-policy"></a>ポリシーを制限

ポリシー*の*制限は、[ここで](../management/restrictedviewaccesspolicy.md)説明しています。

データベースのテーブル上のポリシーを有効または無効にするための制御コマンドは、次のとおりです。

ポリシーを有効/無効にするには::
```kusto
.alter table TableName policy restricted_view_access true|false
```

複数のテーブルのポリシーを有効/無効にするには、次の手順を実行します。
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

ポリシーを表示するには::
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

ポリシーを削除するには(無効にするのと同等です):
```kusto
.delete table TableName policy restricted_view_access  
```