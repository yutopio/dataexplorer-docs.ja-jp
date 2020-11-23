---
title: Null 値-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Null 値について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ac8852adb5138bffe10a4726470b1c53d74cec1b
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324382"
---
# <a name="null-values"></a>null 値

Kusto のすべてのスカラーデータ型には、欠損値を表す特別な値が含まれています。
この値は、 **null 値** として、または単に **null** と呼ばれます。

## <a name="null-literals"></a>null リテラル

スカラー型の null 値は、 `T` null リテラルによってクエリ言語で表され `T(null)` ます。
したがって、次の例では、null の1行が返されます。

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 現時点では、 `string` 型は null 値をサポートしていないことに注意してください。

## <a name="comparing-null-to-something"></a>Null と何かの比較

Null 値は、それ自体を含むデータ型の他の値と等しいかどうかを比較しません。 (つまり、 `null == null` は false です)。値が null 値かどうかを判断するには、 [isnull ()](../isnullfunction.md) 関数および [isnotnull ()](../isnotnullfunction.md) 関数を使用します。

## <a name="binary-operations-on-null"></a>Null に対する二項演算

一般に、null の動作はバイナリ演算子を中心に "固定" される方法です。null 値と他の値 (他の null 値を含む) の間の二項演算では、null 値が生成されます。

## <a name="data-ingestion-and-null-values"></a>データインジェストと null 値

ほとんどのデータ型では、データソースの欠損値によって、対応するテーブルセルに null 値が生成されます。 の例外として、型の列 `string` と CSV 形式のインジェストがあります。この場合、欠損値によって空の文字列が生成されます。
たとえば、次のような場合です。 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

次のことを行います。

|a     |b     |isnull (a)|isempty (a)|strlen (a)|isnull (b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* Kusto エクスプローラーで上記のクエリを実行すると、すべて `true` の値がとして表示され、 `1` すべての `false` 値がとして表示され `0` ます。

* Kusto では、テーブルの列が null 値を持つことを制限する方法は提供されていません (つまり、SQL の制約に相当するものはありません `NOT NULL` )。

::: zone-end

::: zone pivot="azuremonitor"

* Kusto では、テーブルの列が null 値を持つことを制限する方法は提供されていません (つまり、SQL の制約に相当するものはありません `NOT NULL` )。

::: zone-end