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
ms.openlocfilehash: f745d9cb180842e86c184a24ed24c4e2f024f129
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348124"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

現在のレコードが存在するデータシャード ("extent") の[タグ](../management/extents-overview.md#extent-tagging)を持つ動的配列を返します。 

データシャードにアタッチされていない計算データにこの関数を適用すると、空の値が返されます。

## <a name="syntax"></a>構文

`extent_tags()`

## <a name="returns"></a>戻り値

`dynamic`現在のレコードのエクステントタグを保持している配列である型の値、または空の値。

## <a name="examples"></a>例

次の例では、1時間前のレコードを含むすべてのデータシャードのタグの一覧を取得し、その列に特定の値を設定する方法を示し `ActivityId` ます。 この例では、一部のクエリ演算子 (ここでは演算子でもありますが、 `where` とにも当てはまります) を示しています。これは、 `extend` `project` レコードをホストしているデータシャードに関する情報を保持します。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

次の例は、過去1時間のすべてのレコードの数を取得する方法を示しています。これは、タグ `MyTag` (および他のタグ) でタグ付けされていてもタグでタグ付けされていないエクステントに格納されてい `drop-by:MyOtherTag` ます。

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
