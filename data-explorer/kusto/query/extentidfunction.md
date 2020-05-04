---
title: extent_id ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの extent_id () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 300f7961fd11b433ef4e420d5a20b9ad9150b269
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737625"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

現在のレコードが存在するデータシャード ("エクステント") を識別する一意の識別子を返します。 

データシャードにアタッチされていない計算データにこの関数を適用すると、空の guid (すべてゼロ) が返されます。

**構文**

`extent_id()`

**戻り値**

現在のレコードの`guid`データシャードを識別する型の値、または空の guid (すべてゼロ)。

**例**

次の例では、1時間前のレコードを含むすべてのデータシャードの一覧を取得し、その列`ActivityId`に特定の値を設定する方法を示します。 この例では、一部のクエリ演算子 ( `where`ここでは演算子でもあります`extend`が`project`、とにも当てはまります) を示しています。これは、レコードをホストしているデータシャードに関する情報を保持します。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
