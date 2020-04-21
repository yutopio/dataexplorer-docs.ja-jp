---
title: T-SQL - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで T-SQL について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d262d8b7587296c02a2a31d850919af0931bcde6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523411"
---
# <a name="t-sql"></a>T-SQL

Kusto サービスは、いくつかの言語制限を使用して T-SQL クエリを解釈および実行できます。
[Kusto クエリ言語は Kusto](../../query/index.md)の優先言語ですが、このようなサポートは、簡単に変換して優先クエリ言語を使用できない既存のツールや、SQL に精通している人が Kusto を使い慣れた場合に便利です。

> [!NOTE]
> Kusto はこの方法`SELECT`で DDL コマンドをサポートしません。 T-SQL に関する SQL Server と Kusto の主な違いについては、SQL[の既知](./sqlknownissues.md)の問題を参照してください。

## <a name="querying-kusto-from-kustoexplorer-with-t-sql"></a>T-SQL を使用してクストーからクストを照会する.Explorer

Kusto.Explorer ツールは、T-SQL クエリを Kusto に送信できます。
Kusto.Explorer に対してこのモードでクエリを実行するように指示するには、クエリの先頭に空の T-SQL コメント行を追加します。 次に例を示します。

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>T-SQL から Kusto クエリ言語へ

Kusto は、T-SQL クエリを Kusto クエリ言語に翻訳することをサポートしています。 たとえば、KUsto クエリ言語を理解したい SQL の知識を持つ人が使用できます。 一部の T-SQL`SELECT`ステートメントに対応する Kusto クエリ言語`EXPLAIN`を取得するには、クエリの前に追加するだけです。

たとえば、次の T-SQL クエリを使用します。

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

次の出力を生成します。

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```