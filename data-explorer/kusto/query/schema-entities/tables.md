---
title: 表 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのテーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fd47a8f59c716148dfc5f89ac1d4c7aca45a8e9c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509284"
---
# <a name="tables"></a>テーブル

テーブルは、データを保持する名前付きエンティティです。 テーブルには、順序付きの[列](./columns.md)セット 、および 0 行以上のデータがあり、各行はテーブルの各列に対して 1 つのデータ値を保持します。 テーブル内の行の順序は不明であり、一般的にはクエリには影響しませんが、本質的に決定されていない一部の表形式[演算子 (top 演算子](../topoperator.md)など) を除きます。

テーブルは[ストアド関数](./stored-functions.md)と同じ名前空間を占めるため、ストアドファンクションとテーブルの名前が同じ場合は、ストアドファンクションが選択されます。

**メモ**  

* テーブル名では大文字と小文字が区別されます。
* テーブル名は エンティティ[名](./entity-names.md)の規則に従います。
* データベースあたりのテーブルの最大数は 10,000 です。


テーブルの作成と管理の詳細については、[テーブルの管理を参照してください。](../../management/tables.md)

## <a name="table-references"></a>表のリファレンス

テーブルを参照する最も簡単な方法は、テーブル名を使用することです。 これは、コンテキスト内のデータベース内のすべてのテーブルに対して実行できます。 たとえば、次のクエリは、現在のデータベースの`StormEvents`テーブルのレコードをカウントします。

```kusto
StormEvents
| count
```

上記のクエリを書き込むのと同等の方法は、テーブル名をエスケープすることです。

```kusto
["StormEvents"]
| count
```

テーブルは、データベース (またはデータベースとクラスタ) を明示的に示すことで参照でき、複数のデータベースとクラスタのデータを組み合わせたクエリを作成することもできます。 たとえば、次のクエリは、呼び出し元がターゲット データベースにアクセスできる限り、コンテキスト内の任意のデータベースで動作します。

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

table()[特殊関数](../tablefunction.md)を使用してテーブルを参照することもできます。 次に例を示します。

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```