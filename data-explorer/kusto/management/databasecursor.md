---
title: データベース カーソル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのデータベース カーソルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90ec677a7eaf1f326509828b5415b022742fd9ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521320"
---
# <a name="database-cursors"></a>データベース カーソル

**データベース カーソル**はデータベース レベルのオブジェクトであり、クエリと並行して進行中のデータ追加/データ保存操作が進行中であっても、データベースを複数回照会し、一貫性のある結果を得ることができます。

データベース カーソルは、次の 2 つの重要なシナリオに対応するように設計されています。

* クエリが「同じデータセット」を示している限り、同じクエリを複数回繰り返して同じ結果を得ることができます。

* "1 回だけ" クエリを実行する機能 (データが使用できなかったために前のクエリで表示されなかったデータのみを "参照" するクエリ)。
   これにより、たとえば、同じレコードを 2 回処理したり、誤ってレコードをスキップしたりするのを恐れずに、テーブルに新たに到着したすべてのデータを反復処理できます。

データベース カーソルは、クエリ言語で、 type のスカラー値`string`として表されます。 実際の値は不透明と見なされ、その値を保存することや、以下に示すカーソル関数を使用する以外の操作はサポートされません。

## <a name="cursor-functions"></a>カーソル機能

Kusto は、上記の 2 つのシナリオを実装する 3 つの関数を提供します。

* [cursor_current()](../query/cursorcurrent.md): データベース カーソルの現在の値を取得するには、この関数を使用します。
   この値は、他の 2 つの関数の引数として使用できます。
   この関数には、シノニ`current_cursor()`ムという語義語もあります。

* [cursor_after(rhs:string)](../query/cursorafterfunction.md): この特別な関数は、[インジェストタイムポリシー](ingestiontime-policy.md)が有効になっているテーブルレコードに対して使用できます。 レコードのデータベース カーソル値がデータベース`bool`カーソル値の後に`ingestion_time()`来るかどうかを示す、`rhs`種類のスカラー値を返します。

* [cursor_before_or_at(rhs:string)](../query/cursorbeforeoratfunction.md): この特別な関数は、[インジェストタイムポリシー](ingestiontime-policy.md)が有効になっているテーブルレコードで使用できます。 レコードのデータベース カーソル値がデータベース`bool`カーソル値の後に`ingestion_time()`来るかどうかを示す、`rhs`種類のスカラー値を返します。

2 つの特殊関数`cursor_after` `cursor_before_or_at`( および ) にも副作用があります: 使用すると、Kusto は**データベース カーソルの現在の値**をクエリ`@ExtendedProperties`の結果セットに出力します。 カーソルのプロパティ名は`Cursor`、 で、その値は単一`string`です。 次に例を示します。

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>制限

データベース カーソルは、[インジェスションポリシー](ingestiontime-policy.md)が有効になっているテーブルでのみ使用できます。 このようなテーブルの各レコードは、レコードの取り込み時に有効であったデータベースカーソルの値に関連付けられるため[、ingestion_time()](../query/ingestiontimefunction.md)関数を使用できます。

データベース カーソル オブジェクトは[、IngestionTime ポリシー](ingestiontime-policy.md)が定義されているテーブルが少なくとも 1 つある場合を除き、意味のある値を保持しません。
さらに、この値が、このようなテーブルへのインジェスト履歴によって必要に応じて更新され、そのようなテーブルを参照するクエリが実行されるのみ保証されます。 他の場合は更新される場合もあれば、更新されない場合もあります。

取り込みプロセスは、まずデータをコミットし (クエリに使用できるように)、次に各レコードに実際のカーソル値を割り当てます。 つまり、データベース カーソルを使用して取り込み完了の直後にデータを照会しようとすると、カーソル値がまだ割り当てられていないため、最後に追加されたレコードがまだ結果に組み込まれていない可能性があります。 同様に、現在のデータベース カーソル値を繰り返し取得すると、カーソルコミットだけが値を更新するため、(インジェストが間で行われた場合でも) 同じ値が返される可能性があります。

## <a name="example-processing-of-records-exactly-once"></a>例: レコードの正確な 1 回の処理

スキーマ`[Name, Salary]`を`Employees`持つテーブルを想定します。
テーブルに取り込まれる新しいレコードを継続的に処理するには、次の手順に従います。

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