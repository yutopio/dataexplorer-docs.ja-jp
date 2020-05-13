---
title: T-sql-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの T-sql について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1115414358a1025d4931484b81d6eda76109e6cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373528"
---
# <a name="t-sql-support"></a>T-sql のサポート

[Kusto クエリ言語 (KQL)](../../query/index.md)は、推奨されるクエリ言語です。
ただし、t-sql のサポートは、KQL を使用するように簡単に変換できないツールに役立ちます。  
T-sql のサポートは、SQL に精通している方にとっても役に立ちます。

Kusto では、いくつかの言語制限を使用して T-sql クエリを解釈および実行できます。

> [!NOTE]
> Kusto は DDL コマンドをサポートしていません。 `select`サポートされているのは t-sql ステートメントだけです。 T-sql に関する主な相違点の詳細については、「 [sql の既知の問題](./sqlknownissues.md)」を参照してください。

## <a name="querying-from-kustoexplorer-with-t-sql"></a>T-sql を使用した Kusto. エクスプローラーからのクエリ

Kusto エクスプローラーツールでは、Kusto への T-sql クエリがサポートされています。
クエリを実行するように Kusto を指示するには、空の T-sql コメント行 () を使用してクエリを開始します `--` 。 次に例を示します。

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>T-sql から Kusto クエリ言語へ

Kusto は、T-sql クエリの Kusto クエリ言語 (KQL) への変換をサポートしています。 この翻訳は、SQL を使い慣れている方が KQL の理解を深めるのに役立ちます。
一部の T-sql ステートメントから同等の KQL を取得するには `select` 、 `explain` クエリの前にを追加します。

たとえば、次の T-sql クエリを使用します。

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

次の出力が生成されます。

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```
