---
title: .alter データベースのプリティネーム - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .alter データベースのプリティネームについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b362b547dc980676108ec169a0abb97f189375b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522595"
---
# <a name="alter-database-prettyname"></a>.alter データベースのプリティネーム

データベースの可愛い (フレンドリな) 名前を変更します。

[データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.alter``database`*データベース*`prettyname`名`'`*データベースの可愛い名前*`'`

**出力を返す**
 
|出力パラメーター |Type |説明 
|---|---|---
|DatabaseName |String |データベースの名前。
|プリティネーム |String |データベースの名前を指定します。

