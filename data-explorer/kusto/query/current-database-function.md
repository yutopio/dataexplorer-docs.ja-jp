---
title: current_database() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの current_database() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c61717bbc8d202624b36088df5aed2ba3f3a8d2d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516747"
---
# <a name="current_database"></a>current_database()

スコープ内のデータベースの名前 (他のデータベースが指定されていない場合にすべてのクエリ エンティティが解決されるデータベース) を返します。

**構文**

`current_database()`

**戻り値**

スコープ内のデータベースの名前を type の値`string`として指定します。

**例**

```kusto
print strcat("Database in scope: ", current_database())
```