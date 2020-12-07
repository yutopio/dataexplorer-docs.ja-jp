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
ms.openlocfilehash: 9dc2e1bf489123ddd66c9a203d74284e36eda53e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320946"
---
# <a name="databases-management"></a>データベース管理

このトピックでは、次のデータベース制御コマンドについて説明します。

|コマンド |説明 |
|--------|------------|
|[`.show databases`](show-databases.md) |すべてのレコードが、ユーザーがアクセスできるクラスター内のデータベースに対応するテーブルを返します。|
|[`.show database`](show-database.md) |コンテキストデータベースのプロパティを示すテーブルを返します。 |
|[`.show cluster databases`](show-cluster-database.md) |クラスターにアタッチされ、コマンドを呼び出しているユーザーがアクセスできるすべてのデータベースを示すテーブルを返します。 |
|[`.alter database`](alter-database.md) |データベースの非常にわかりやすい名前を変更します。 |
|[`.show database schema`](show-schema-database.md) |1つのテーブルまたは JSON オブジェクト内のすべてのテーブルと列を含む、選択されたデータベースの構造の単純なリストを返します。 |