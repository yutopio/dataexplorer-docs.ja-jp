---
title: データベースカーソル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのデータベースカーソルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75dc0aa0ff23bfb4f08be9fac84fa34cf9526508
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780628"
---
# <a name="database-cursors"></a>データベースカーソル

データベース**カーソル**はデータベースレベルのオブジェクトであり、データベースに対して複数回クエリを実行できます。 クエリと並行して実行されているまたは操作がある場合でも、一貫性のある結果が得られ `data-append` `data-retention` ます。

データベースカーソルは、2つの重要なシナリオに対応するように設計されています。

* クエリで "同じデータセット" が指定されている限り、同じクエリを複数回繰り返し、同じ結果を得ることができます。

* "厳密に1回" のクエリを作成する機能。 このクエリでは、前のクエリで参照されていなかったデータが "表示" されるだけです。これは、データが使用できなかったためです。
   このクエリを使用すると、たとえば、テーブル内の新しく到着したすべてのデータを反復処理できます。同じレコードを2回処理したり、誤ってレコードをスキップしたりする心配はありません。

データベースカーソルは、クエリ言語では型のスカラー値として表され `string` ます。 実際の値は非透過的と見なされ、その値を保存したり、以下に示すカーソル関数を使用したりする以外の操作はサポートされません。

## <a name="cursor-functions"></a>カーソル関数

Kusto には、上記の2つのシナリオを実装するのに役立つ3つの関数が用意されています。

* [cursor_current ()](../query/cursorcurrent.md): データベースカーソルの現在の値を取得するには、この関数を使用します。
   この値は、他の2つの関数の引数として使用できます。
   この関数には、シノニムもあり `current_cursor()` ます。

* [cursor_after (rhs: string)](../query/cursorafterfunction.md): この特別な関数は、 [IngestionTime ポリシー](ingestiontime-policy.md)が有効になっているテーブルレコードで使用できます。 このメソッドは、 `bool` レコードの `ingestion_time()` データベースカーソル値がデータベースカーソル値の後にあるかどうかを示す型のスカラー値を返し `rhs` ます。

* [cursor_before_or_at (rhs: string)](../query/cursorbeforeoratfunction.md): [IngestionTime ポリシー](ingestiontime-policy.md)が有効になっているテーブルレコードで、この特別な関数を使用できます。 このメソッドは、 `bool` レコードの `ingestion_time()` データベースカーソル値がデータベースカーソル値の後にあるかどうかを示す型のスカラー値を返し `rhs` ます。

2つの特殊な関数 ( `cursor_after` と `cursor_before_or_at` ) も、副作用があります。これを使用すると、Kusto は、**データベースカーソルの現在の値**を `@ExtendedProperties` クエリの結果セットに出力します。 カーソルのプロパティ名は `Cursor` であり、その値は単一 `string` です。 

次に例を示します。

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>制限

データベースカーソルは、 [IngestionTime ポリシー](ingestiontime-policy.md)が有効になっているテーブルでのみ使用できます。 このようなテーブル内の各レコードは、レコードが取り込まれたされたときに有効だったデータベースカーソルの値に関連付けられています。
そのため、 [ingestion_time ()](../query/ingestiontimefunction.md)関数を使用できます。

データベースに[IngestionTime ポリシー](ingestiontime-policy.md)が定義されているテーブルが少なくとも1つある場合を除き、データベースカーソルオブジェクトには意味のある値が保持されません。
この値は、このようなテーブルを参照するテーブルやクエリが実行されるように、インジェスト履歴によって必要に応じて更新されることが保証されます。 それ以外の場合は、更新される可能性があります。

インジェストプロセスでは、まずデータがコミットされ、クエリに使用できるようになります。その後、各レコードに実際のカーソル値が割り当てられます。 データベースカーソルを使用してインジェストの完了直後にデータのクエリを実行しようとすると、カーソル値がまだ割り当てられていないため、最後に追加されたレコードが結果に組み込まれていない可能性があります。 また、現在のデータベースカーソル値を繰り返し取得すると、その値を更新できるのはカーソルコミットだけなので、その間にインジェストが行われた場合でも、同じ値が返される可能性があります。

## <a name="example-processing-records-exactly-once"></a>例: 1 回だけレコードを処理する

スキーマを含むテーブルでは、 `Employees` `[Name, Salary]` テーブルに取り込まれたするときに新しいレコードを継続的に処理するために、次のプロセスを使用します。

```kusto
// [Once] Enable the IngestionTime policy on table Employees
.set table Employees policy ingestiontime true

// [Once] Get all the data that the Employees table currently holds 
Employees | where cursor_after('')

// The query above will return the database cursor value in
// the @ExtendedProperties result set. Lets assume that it returns
// the value '636040929866477946'

// [Many] Get all the data that was added to the Employees table
// since the previous query was run using the previously-returned
// database cursor 
Employees | where cursor_after('636040929866477946') // -> 636040929866477950

Employees | where cursor_after('636040929866477950') // -> 636040929866479999

Employees | where cursor_after('636040929866479999') // -> 636040939866479000
```
