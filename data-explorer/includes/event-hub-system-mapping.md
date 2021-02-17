---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: bd12845234ec57c1393935b7366b8d2e387b3890
ms.sourcegitcommit: d19b4214625eeb1ec7aec4fd6c92007a07c76ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2020
ms.locfileid: "99554829"
---
> [!NOTE]
> * システム プロパティは、圧縮データではサポートされていません。
> * 表形式データの場合、システム プロパティは単一レコードのイベント メッセージでのみサポートされます。
> * JSON データの場合、システム プロパティは複数レコードのイベント メッセージでもサポートされます。 このような場合、システム プロパティは、イベント メッセージの最初のレコードにのみ追加されます。 
> * `csv` マッピングの場合、[システム プロパティ](../ingest-data-event-hub-overview.md#system-properties)の表に示された順序でプロパティがレコードの先頭に追加されます。
> * `json` マッピングの場合、[システム プロパティ](../ingest-data-event-hub-overview.md#system-properties)の表のプロパティ名に従ってプロパティが追加されます。
