---
title: Null 値 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの Null 値について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c3a48fdfca855a1ff2f848d4ed97d8162e97b931
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765944"
---
# <a name="null-values"></a>null 値

Kusto のすべてのスカラー データ型には、欠損値を表す特別な値があります。
この値は**null 値**、または単に null と呼**ばれます**。

## <a name="null-literals"></a>NULL リテラル

スカラー型`T`の null 値は、クエリ言語では null リテラル`T(null)`で表されます。
したがって、次の例では、NULL 値の完全な単一行が返されます。

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 現在、この型は`string`null 値をサポートしていません。

## <a name="comparing-null-to-something"></a>null を何かに比較する

null 値は、それ自体を含むデータ型の他の値と等しい比較しません。 (つまり、`null == null`偽です。値がヌル値かどうかを判別するには[、isnull()](../isnullfunction.md)関数と[isnotnull()](../isnotnullfunction.md)関数を使用します。

## <a name="binary-operations-on-null"></a>NULL に対するバイナリ演算

一般に、null は二項演算子を囲む"スティッキー"な方法で動作します。NULL 値と他の値 (別の NULL 値を含む) の間のバイナリ演算では、NULL 値が生成されます。

## <a name="data-ingestion-and-null-values"></a>データ取り込みと NULL 値

ほとんどのデータ型では、データ ソース内の値が不足していると、対応するテーブル セルに null 値が生成されます。 例外は、型`string`と CSV のようなインジェストの列で、欠損値が空の文字列を生成します。
たとえば、次の場合は、次の場合に使用します。 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

その後、以下を実行します。

|a     |b     |isnull(a)|は空です(a)|ストレン(a)|null(b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |true     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* 上記のクエリを Kusto.Explorer で実行すると`true`、すべての値が`1`として削除され、すべての`false`値が`0`として表示されます。

::: zone-end

* Kusto は、テーブルの列に NULL 値を設定しないように制限する方法を提供していません (つまり、SQL の`NOT NULL`制約と同等の値はありません)。
