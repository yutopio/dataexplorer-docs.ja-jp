---
title: . alter database の名前の変更-Azure データエクスプローラー
description: この記事では、データベースの愛称のコマンドについて説明し `.alter` ます。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fc445a7d85f52d672b92163cc358d8f3a741c68
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294510"
---
# <a name="alter-database-prettyname"></a>. alter database の名前を変更します。

データベースの非常にわかりやすい名前を変更します。

[Databaseadmin 権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.alter``database` *DatabaseName* `prettyname` DatabaseName `'`*Databaseて Tyname*`'`

**出力を返す**
 
|出力パラメーター |Type |説明 
|---|---|---
|DatabaseName |String |データベースの名前
|"この名前" |String |データベースの名前 (愛称)
