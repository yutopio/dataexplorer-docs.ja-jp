---
title: 格納されている関数の管理の概要-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのストアド関数管理の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4354538b206ee86e34941718bcf6d74130647fb6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321354"
---
# <a name="stored-functions-management-overview"></a>ストアド関数の管理の概要
ここでは、次の [ユーザー定義関数](../query/functions/user-defined-functions.md)を作成および変更するために使用されるコントロールコマンドについて説明します。

|機能 |説明|
|---------|-----------|
|[`.alter function`](alter-function.md) |既存の関数を変更し、データベースメタデータ内に格納します。 |
|[`.alter function docstring`](alter-docstring-function.md) |既存の関数の DocString 値を変更します。 |
|[`.alter function folder`](alter-folder-function.md) |既存の関数のフォルダー値を変更します。 |
|[`.create function`](create-function.md) |格納されている関数を作成します。 |
|[`.create-or-alter function`](create-alter-function.md) |格納されている関数を作成するか、既存の関数を変更してデータベースメタデータ内に格納します。 |
|[`.drop function` そして `.drop functions`](drop-function.md) |データベースから関数 (または関数) を削除します。 |
|[`.show functions` そして `.show function`](show-function.md) |現在選択されているデータベースに格納されているすべての関数、または特定の関数を一覧表示します。 |