---
title: 列の削除-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの drop 列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 770f787493828e897600485c282f3ccb12bb74c4
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966875"
---
# <a name="drop-column"></a>.drop column

テーブルから列を削除します。
テーブルから複数の列を削除するには、[以下](#drop-table-columns)を参照してください。

> [!WARNING]
> このコマンドは元に戻すことができません。 削除される列のすべてのデータが削除されます。
> 今後この列を追加するコマンドでは、データを復元することはできません。

**構文**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>テーブル列の削除

テーブルから複数の列を削除します。

> [!WARNING]
> このコマンドは元に戻すことができません。 削除された列のすべてのデータが削除されます。
> 今後、これらの列を追加するコマンドは、データを復元することはできません。

**構文**

`.drop``table` *TableName* `columns` TableName `(`*Col1* [ `,` *Col2*]...`)`