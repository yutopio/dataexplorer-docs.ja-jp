---
title: データベース - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのデータベースについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a94b168d8aa8443251f3a01dc659e114b0aacaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509437"
---
# <a name="databases"></a>データベース

Kusto は、上位レベルのエンティティが`database`. 

Kusto クラスタは複数のデータベースをホストでき、各データベースは独自の[テーブル](tables.md)、[ストアドファンクション](stored-functions.md)、および[外部テーブル](externaltables.md)のコレクションをホストします。
各データベースには、[ロール ベースのアクセス制御 (RBAC) モデル](../../management/access-control/index.md)に基づいて、独自のアクセス許可セットがあります。

**メモ**  

* データベース名は大文字と小文字を区別しません。
* データベース名は[、 エンティティ名](./entity-names.md)の規則に従う必要があります。
* クラスターあたりのデータベースの最大数は 10,000 です。