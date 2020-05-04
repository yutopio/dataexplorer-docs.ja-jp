---
title: extent_tags ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの extent_tags () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 146ab59c1c0cbcb86bfae94fa26c09f5afa0f216
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737591"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

現在のレコードが存在するデータシャード ("extent") の[タグ](../management/extents-overview.md#extent-tagging)を持つ動的配列を返します。 

データシャードにアタッチされていない計算データにこの関数を適用すると、空の値が返されます。

**構文**

`extent_tags()`

**戻り値**

現在のレコードの`dynamic`エクステントタグを保持している配列である型の値、または空の値。

**使用例**

次の例では、1時間前のレコードを含むすべてのデータシャードのタグの一覧を取得し、その列`ActivityId`に特定の値を設定する方法を示します。 この例では、一部のクエリ演算子 ( `where`ここでは演算子でもあります`extend`が`project`、とにも当てはまります) を示しています。これは、レコードをホストしているデータシャードに関する情報を保持します。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

次の例は、過去1時間のすべてのレコードの数を取得する方法を示しています。これは、タグ`MyTag` (および他のタグ) でタグ付けされてい`drop-by:MyOtherTag`てもタグでタグ付けされていないエクステントに格納されています。

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
