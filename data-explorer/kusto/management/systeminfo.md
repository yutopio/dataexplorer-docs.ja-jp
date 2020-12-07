---
title: システム情報-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのシステム情報について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/07/2019
ms.openlocfilehash: 22ad4956d94f445b26e49e1e73ed54e86568d1ad
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321218"
---
# <a name="system-information"></a>システム情報

このセクションでは、およびロールで使用可能なコマンドの概要を説明し `Database Admins` `Database Monitors` 、使用状況を調査し、操作を追跡し、インジェストの失敗を調査します。

* [`.show queries`](queries.md) -完了したクエリと実行中のクエリに関する情報を表示します。
* [`.show commands`](commands.md) -完了したコマンドとそのリソース使用率に関する情報を表示します。
* [`.show commands-and-queries`](commands-and-queries.md) -完了したコマンドとクエリ、およびそれらのリソース使用率に関する情報を表示します。
* [`.show journal`](journal.md) -メタデータ操作の履歴を表示します。
* [`.show operations`](operations.md) -管理ノードが最後に選択されたため、[実行中] と [完了] の両方の管理操作が表示されます。
* [`.show failed ingestions`](ingestionfailures.md) -クラスターへのデータの取り込み中に発生したエラーに関する情報を表示します。