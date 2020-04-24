---
title: ドロップ列 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのドロップ列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 56ba5499b27b517b3080ee27ac317aa1e0917edd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521218"
---
# <a name="drop-column"></a>列を削除する

テーブルから列を削除します。
テーブルから複数の列を削除するには、[以下](#drop-table-columns)のを参照してください。

> [!WARNING]
> このコマンドは元に戻せません。 削除された列のすべてのデータが削除されます。
> その列を追加するコマンドは、データを復元できません。

**構文**

`.drop``column`*テーブル名*`.`*列名*

## <a name="drop-table-columns"></a>テーブル列を削除する

テーブルからいくつかの列を削除します。

> [!WARNING]
> このコマンドは元に戻せません。 削除された列のすべてのデータが削除されます。
> これらの列を追加するコマンドは、データを復元できません。

**構文**

`.drop``table`*テーブル*`columns`名`(`*コル1* `,` [*コル2].*`)`