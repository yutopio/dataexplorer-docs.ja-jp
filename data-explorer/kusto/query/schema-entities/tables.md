---
title: テーブル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのテーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f51e05abac44b85ab328e7df5645eeab51d2a274
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550624"
---
# <a name="tables"></a>テーブル

テーブルは、データを保持する名前付きエンティティです。 テーブルには、順序付けされた一連の[列](./columns.md)と、0個以上のデータ行があります。 各行には、テーブルの列ごとに1つのデータ値が保持されます。 テーブル内の行の順序は不明であり、一般にはクエリに影響しません。ただし、本質的に未確定の表形式演算子 ( [top 演算子](../topoperator.md)など) がある場合を除きます。

テーブルは、[格納され](./stored-functions.md)ている関数と同じ名前空間を占有します。
格納されている関数とテーブルの両方に同じ名前が指定されている場合は、格納されている関数が選択されます。

**メモ**  

* テーブル名は大文字と小文字が区別されます。
* テーブル名は、[エンティティ名](./entity-names.md)の規則に従います。
* データベースごとのテーブルの上限は1万です。


テーブルを作成および管理する方法の詳細については、「[テーブルの管理](../../management/tables.md)」を参照してください。

## <a name="table-references"></a>テーブル参照

テーブルを参照する最も簡単な方法は、その名前を使用することです。 この参照は、コンテキスト内のデータベース内のすべてのテーブルに対して実行できます。 たとえば、次のクエリでは、現在のデータベースのテーブルのレコードがカウントされ `StormEvents` ます。

```kusto
StormEvents
| count
```

上記のクエリを記述するには、テーブル名をエスケープする方法もあります。

```kusto
["StormEvents"]
| count
```

テーブルは、データベース (またはデータベースとクラスター) を明示的にメモして参照することもできます。 その後、複数のデータベースとクラスターのデータを結合するクエリを作成できます。 たとえば、次のクエリは、呼び出し元がターゲットデータベースにアクセスできる限り、コンテキスト内の任意のデータベースで動作します。

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

また、 [table () 特別な関数](../tablefunction.md)を使用してテーブルを参照することもできます。ただし、その関数の引数が定数に評価される場合に限ります。 次に例を示します。

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```