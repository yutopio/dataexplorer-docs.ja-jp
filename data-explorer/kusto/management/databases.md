---
title: データベース管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのデータベース管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 78fcba2db059c0115d65032610009ab20bd2d5bd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521286"
---
# <a name="databases-management"></a>データベース管理

このトピックでは、次のデータベース制御コマンドについて説明します。

|command |説明 |
|--------|------------|
|[.show データベース](show-databases.md) |ユーザーがアクセスできるクラスタ内のデータベースに対応する、すべてのレコードが含まれるテーブルを返します。|
|[.show データベース](show-database.md) |コンテキスト データベースのプロパティを示すテーブルを返します。 |
|[.show クラスタ データベース](show-cluster-database.md) |クラスタにアタッチされているデータベースと、コマンドを呼び出すユーザーがアクセスできるすべてのデータベースを示すテーブルを返します。 |
|[データベースの変更](alter-database.md) |データベースの可愛い (わかりやすい) 名前を変更します。 |
|[.drop データベース](drop-database.md) |データベースの可愛い (わかりやすい) 名前を削除します。 |
|[.show データベース スキーマ](show-schema-database.md) |選択したデータベースの構造のフラット リストを返し、そのすべてのテーブルと列を 1 つのテーブルまたは JSON オブジェクトに格納します。 |