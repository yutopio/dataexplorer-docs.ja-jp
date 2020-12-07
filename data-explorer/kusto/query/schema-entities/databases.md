---
title: データベース-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのデータベースについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 3d981a4b56eef689081a4bc6a622221c126efa2e
ms.sourcegitcommit: 8b8228fe18e6408673891374a8048a7a3723921c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96516066"
---
# <a name="databases"></a>データベース

Kusto は、上位レベルのエンティティがであるデータを格納するリレーションモデルに従い `database` ます。 

Kusto クラスターは複数のデータベースをホストできます。各データベースは、独自の [テーブル](tables.md)、 [格納さ](stored-functions.md)れた関数、および [外部テーブル](externaltables.md)のコレクションをホストします。
各データベースには、[ロールベースの Access Control (RBAC) モデル](../../management/access-control/index.md)に基づいて独自の権限が設定されています。

**メモ**  

* データベース名は、 [エンティティ名](./entity-names.md)の規則に従っている必要があります。
* クラスターあたりのデータベースの上限は1万です。
