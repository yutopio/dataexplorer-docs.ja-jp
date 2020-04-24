---
title: .drop データベースのプリティネーム - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .drop データベースのプリティネームについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8a74b871ddcf420f39bb45ebdf3bce36f026105
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521184"
---
# <a name="drop-database-prettyname"></a>.drop データベースのプリティネーム

データベースの可愛い (わかりやすい) 名前を削除します。
[データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.drop` `database` *DatabaseName* `prettyname`

**出力を返す**
 
|出力パラメーター |Type |説明 
|---|---|---
|DatabaseName |String |データベースの名前です。
|プリティネーム |String |データベースの名前 (ドロップ操作後に null)