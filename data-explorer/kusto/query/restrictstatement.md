---
title: Restrict ステートメント-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Restrict ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 094cec5b467c35eb9dbeeb756362bd13c77873ce
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737778"
---
# <a name="restrict-statement"></a>restrict ステートメント

::: zone pivot="azuredataexplorer"

Restrict ステートメントは、その後に続くクエリステートメントで参照できるテーブル/ビューエンティティのセットを制限します。 たとえば、2つのテーブル`A`(、 `B`) を含むデータベースでは、アプリケーションがクエリの残りの部分にアクセス`B`できないようにし、ビューを使用して`A`限られた形式のテーブルだけを表示することができます。

Restrict ステートメントの主なシナリオは、ユーザーからのクエリを受け入れ、それらのクエリに行レベルのセキュリティメカニズムを適用する中間層アプリケーションの場合です。 中間層アプリケーションは、ユーザーのクエリにプレフィックスとして**論理モデル**を付けることができます。これは、ユーザーのデータへのアクセスを制限するビュー `T | where UserId == "..."`を定義する let ステートメントのセットです (たとえば、)。 最後に追加されたステートメントとして、ユーザーのアクセスを論理モデルのみに制限します。

**構文**

`restrict``access` `,` [Entityspecifier 子 [...]]*EntitySpecifier* `to` `(``)`

*Entityspecifier 子*は次のいずれかになります。
* Let ステートメントで表形式ビューとして定義された識別子。
* テーブル参照 (union ステートメントで使用されているものと似ています)。
* パターン宣言によって定義されるパターン。

Restrict ステートメントで指定されていないすべてのテーブル、表形式ビュー、またはパターンは、クエリの残りの部分で "非表示" になります。 

**メモ**

Restrict ステートメントを使用して、別のデータベースまたはクラスター内のエンティティへのアクセスを制限することができます (クラスター名ではワイルドカードはサポートされていません)。

**引数**

Restrict ステートメントでは、エンティティの名前解決時に制限の制限を定義するパラメーターを1つ以上取得できます。 エンティティは次のようになります。
- [let statement](./letstatement.md)ステートメントの前に`restrict`ステートメントを記述します。 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- データベースメタデータで定義されている[テーブル](../management/tables.md)または[関数](../management/functions.md)。

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- [Let ステートメント](./letstatement.md)またはテーブル/関数の倍数に一致するワイルドカードパターン  

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

次の例では、中間層アプリケーションが、ユーザーが他のユーザーのデータを照会できないようにする論理モデルを使用して、ユーザーのクエリを前に付加する方法を示します。

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

この機能は、ではサポートされていません Azure Monitor

::: zone-end
