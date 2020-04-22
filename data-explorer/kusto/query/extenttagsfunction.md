---
title: extent_tags() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで extent_tags() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d8af7e51c5e2efb16763541db05e9ccc7e2cb95f
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765429"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

現在のレコードが存在するデータ シャード ("エクステント") の[タグ](../management/extents-overview.md#extent-tagging)を持つ動的配列を返します。 

データシャードにアタッチされていない計算データにこの関数を適用すると、空の値が返されます。

**構文**

`extent_tags()`

**戻り値**

現在のレコードの`dynamic`エクステント タグを保持する配列、または空の値を表す型の値。

**使用例**

次の例は、1 時間前のレコードを持つすべてのデータ シャードのタグを、列に固有の値を持つリスト`ActivityId`を取得する方法を示しています。 一部のクエリ演算子 (ここでは`where`演算子ですが、`extend``project`と ) はレコードをホストするデータ シャードに関する情報を保持する方法を示します。

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

次の例では、タグ (および場合によっては他のタグ) でタグ付けされたエクステントに格納され、タグ`MyTag``drop-by:MyOtherTag`を付けられません、過去 1 時間のすべてのレコードの数を取得する方法を示します。

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
