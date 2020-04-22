---
title: 制限ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの制限ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cbd21c01956f817c5db19a93104028dba2b2b2b4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765992"
---
# <a name="restrict-statement"></a>restrict ステートメント

::: zone pivot="azuredataexplorer"

restrict ステートメントは、その後に続くクエリステートメントに表示される一連のテーブル/ビュー エンティティを制限します。 たとえば、2 つのテーブル`A`( 、 )`B`を含むデータベースでは、アプリケーションはクエリの残りの`B`部分がアクセスできないようにし、ビューを使用して`A`制限された形式のテーブルのみを 「参照」できます。

Restrict ステートメントの主なシナリオは、ユーザーからのクエリを受け付け、それらのクエリに行レベルのセキュリティ メカニズムを適用する中間層アプリケーション用です。 中間層アプリケーションは、ユーザーのクエリの前に**論理モデル**を付けることができます`T | where UserId == "..."`。 最後に追加されるステートメントとして、ユーザーの論理モデルへのアクセスのみが制限されます。

**構文**

`restrict``access` `,` [*エンティティスペシビアー* [ .]] `to` `(``)`

*エンティティの仕様子*は、次のいずれかです。
* let ステートメントによって表形式ビューとして定義される識別子。
* テーブル参照 (共用体ステートメントで使用される参照と同様)。
* パターン宣言によって定義されるパターン。

restrict ステートメントで指定されていないすべてのテーブル、表形式のビュー、またはパターンは、クエリの残りの部分に対して "非表示" になります。 

**メモ**

restrict ステートメントを使用して、別のデータベースまたはクラスター内のエンティティへのアクセスを制限できます (ワイルドカードはクラスター名ではサポートされません)。

**引数**

restrict ステートメントは、エンティティの名前解決時に許容制限を定義する 1 つ以上のパラメーターを取得できます。 エンティティは次の場合があります。
- [ステートメントの](./letstatement.md)前`restrict`にステートメントを記述します。 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- データベース メタデータで定義されている[テーブル](../management/tables.md)または[関数](../management/functions.md)。

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- [let ステートメント](./letstatement.md)またはテーブル/関数の複数の値に一致するワイルドカード パターン  

```kusto
let Test1 = () { print x=1 };
let Test2 = () { print y=1 };
restrict access to (*);
// Now access is restricted to Test1, Test2 and no tables/functions are accessible.

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database().*);
// Now access is restricted to all tables/functions of the current database ('DB2' is not accessible).

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database('DB2').*);
// Now access is restricted to all tables/functions of the database 'DB2'
```


**使用例**

次の例は、中間層アプリケーションが、ユーザーが他のユーザーのデータを照会できないようにする論理モデルを備えたユーザーのクエリを追加する方法を示しています。

```kusto
// Assume the database has a single table, UserData,
// with a column called UserID and other columns that hold
// per-user private information.
//
// The middle-tier application generates the following statements.
// Note that "username@domain.com" is something the middle-tier application
// derives per-user as it authenticates the user.
let RestrictedData = view () { Data | where UserID == "username@domain.com" };
restrict access to (RestrictedData);
// The rest of the query is something that the user types.
// This part can only reference RestrictedData; attempting to reference Data
// will fail.
RestrictedData | summarize IrsLovesMe=sum(Salary) by Year, Month
```

```kusto
// Restricting access to Table1 in the current database (database() called without parameters)
restrict access to (database().Table1);
Table1 | count

// Restricting acess to Table1 in the current database and Table2 in database 'DB2'
restrict access to (database().Table1, database('DB2').Table2);
union 
    (Table1),
    (database('DB2').Table2))
| count

// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
