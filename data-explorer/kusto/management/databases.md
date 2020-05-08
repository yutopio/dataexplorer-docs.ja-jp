---
title: データベース管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータベース管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 03d21dc76bbffb72275a35c86bb8030942228184
ms.sourcegitcommit: addc4eb50ae65240975d63292e9f6907a74f5dfe
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966742"
---
# <a name="databases-management"></a>データベース管理

このトピックでは、次のデータベース制御コマンドについて説明します。

|command |説明 |
|--------|------------|
|[。データベースを表示します。](show-databases.md) |すべてのレコードが、ユーザーがアクセスできるクラスター内のデータベースに対応するテーブルを返します。|
|[.show database](show-database.md) |コンテキストデータベースのプロパティを示すテーブルを返します。 |
|[。クラスターデータベースを表示します。](show-cluster-database.md) |クラスターにアタッチされ、コマンドを呼び出しているユーザーがアクセスできるすべてのデータベースを示すテーブルを返します。 |
|[. alter database](alter-database.md) |データベースの非常にわかりやすい名前を変更します。 |
|[.show database schema](show-schema-database.md) |1つのテーブルまたは JSON オブジェクト内のすべてのテーブルと列を含む、選択されたデータベースの構造の単純なリストを返します。 |