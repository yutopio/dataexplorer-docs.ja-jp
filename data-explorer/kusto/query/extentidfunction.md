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
ms.openlocfilehash: 22f1b36b851c6e629abd2524feb4c40c74bbb1fa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348141"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

現在のレコードが存在するデータシャード ("エクステント") を識別する一意の識別子を返します。

データシャードにアタッチされていない計算データにこの関数を適用すると、空の guid (すべてゼロ) が返されます。

## <a name="syntax"></a>構文

`extent_id()`

## <a name="returns"></a>戻り値

`guid`現在のレコードのデータシャードを識別する型の値、または空の guid (すべてゼロ)。

## <a name="example"></a>例

次の例では、1時間前のレコードを含むすべてのデータシャードの一覧を取得し、その列に特定の値を設定する方法を示し `ActivityId` ます。 この例では、一部のクエリ演算子 (ここでは、 `where` 演算子、および `extend` およびも) で `project` 、レコードをホストしているデータシャードに関する情報を保持しています。

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
