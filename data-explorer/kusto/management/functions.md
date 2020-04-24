---
title: ストアドファンクション管理の概要 - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのストアド関数管理の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f3768c6252a96215d37bd9f19a44cbf4d3afc731
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520963"
---
# <a name="stored-functions-management-overview"></a>ストアドファンクション管理機能の概要
この節では、以下の[ユーザー定義関数](../query/functions/user-defined-functions.md)を作成および変更するために使用する制御コマンドについて説明します。

|機能 |説明|
|---------|-----------|
|[変更機能](alter-function.md) |既存の関数を変更し、データベースメタデータ内に格納します。 |
|[変更関数のドキュメント文字列](alter-docstring-function.md) |既存の関数の DocString 値を変更します。 |
|[.alter 関数フォルダ](alter-folder-function.md) |既存の関数の Folder 値を変更します。 |
|[作成機能](create-function.md) |格納された関数を作成します。 |
|[作成または変更関数](create-alter-function.md) |ストアドファンクションを作成するか、既存の関数を変更してデータベースメタデータに格納します。 |
|[.drop 関数と .drop 関数](drop-function.md) |データベースから関数 (または関数) を削除します。 |
|[.show 関数と .show 関数](show-function.md) |現在選択されているデータベース内のすべてのストアド関数または特定の関数を一覧表示します。 |