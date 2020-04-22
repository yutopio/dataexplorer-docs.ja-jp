---
title: データテーブルオペレータ - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでデータテーブル演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 182f8030e3263ee5bf6bee4ca7444b0d5596e7d6
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765385"
---
# <a name="datatable-operator"></a>datatable 演算子

クエリ自体でスキーマと値が定義されているテーブルを返します。

この演算子にパイプラインの入力はないことに注意してください。

**構文**

`datatable``(`*列名*`:`*列タイプ*`,` [ ..]`)`*ScalarValue*`,`*ScalarValue*スカラ値 [スカラ値.] `[``]`

**引数**

::: zone pivot="azuredataexplorer"

* *列名*、*列型*: テーブルのスキーマを定義します。 使用される構文は、テーブルを定義する際に使用する構文とまったく同じです ( [.create table](../management/create-table-command.md)を参照)。
* *ScalarValue*: テーブルに挿入する定数スカラー値。 値の数は、テーブル内の列の整数倍である必要があり *、n*'th の値は列*n* % *NumColumns*に対応する型を持つ必要があります。

::: zone-end

::: zone pivot="azuremonitor"

* *列名*、*列型*: テーブルのスキーマを定義します。
* *ScalarValue*: テーブルに挿入する定数スカラー値。 値の数は、テーブル内の列の整数倍である必要があり *、n*'th の値は列*n* % *NumColumns*に対応する型を持つ必要があります。

::: zone-end

**戻り値**

この演算子は、指定されたスキーマとデータのデータ テーブルを返します。

**例**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
