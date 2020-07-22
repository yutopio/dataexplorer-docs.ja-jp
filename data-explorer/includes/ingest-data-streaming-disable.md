---
title: インクルード ファイル
description: インクルード ファイル
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: a748332c85839bac8466532ba3959810a6387834
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423069"
---
## <a name="disable-streaming-ingestion-on-your-cluster"></a>クラスターでストリーミング インジェストを無効にする

> [!WARNING]
> ストリーミング インジェストの無効化には数時間かかることがあります。

Azure Data Explorer クラスターでストリーミング インジェストを無効にする前に、関連するすべてのテーブルとデータベースから[ストリーミング インジェスト ポリシー](../kusto/management/streamingingestionpolicy.md)を削除します。 ストリーミング インジェスト ポリシーを削除すると、Azure Data Explorer クラスター内でデータの再配置がトリガーされます。 ストリーミング インジェストのデータは、初期ストレージから列ストア (エクステントまたはシャード) 内の永続的なストレージへ移動されます。 初期ストレージ内のデータ量によっては、このプロセスに数秒から数時間かかることがあります。
