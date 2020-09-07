---
title: 外部 SQL テーブルの作成と変更-Azure データエクスプローラー
description: この記事では、外部 SQL テーブルを作成および変更する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 79816960b75735e226395f70286ea9d81829a173
ms.sourcegitcommit: 08c54dabc1efe3d4e2d2581c4b668a6b73daf855
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2020
ms.locfileid: "89510697"
---
# <a name="create-and-alter-external-sql-tables"></a>外部 SQL テーブルを作成および変更する

コマンドが実行されるデータベース内の外部 SQL テーブルを作成または変更します。  

## <a name="syntax"></a>構文

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *TableName* ([columnName: columnType],...)  
`kind` `=` `sql`  
`table``=` *Sqltablename*  
`(`*SqlServerConnectionString*`)`  
[ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*]、 *property_name* `=` *値* `,` ... `)` ]

## <a name="parameters"></a>パラメーター

* *TableName* -外部テーブル名。 [エンティティ名](../query/schema-entities/entity-names.md)の規則に従う必要があります。 外部テーブルは、同じデータベース内の通常のテーブルと同じ名前にすることはできません。
* *Sqltablename* -SQL テーブルの名前。
* *SqlServerConnectionString* -SQL server への接続文字列。 次のいずれかのメソッドを指定できます。 
  * *Aad 統合認証* ( `Authentication="Active Directory Integrated"` ): ユーザーまたはアプリケーションが Aad 経由で kusto に対して認証を行い、同じトークンを使用して SQL Server ネットワークエンドポイントにアクセスします。
  * *ユーザー名/パスワード認証* ( `User ID=...; Password=...;` )。 外部テーブルを [連続エクスポート](data-export/continuous-data-export.md)に使用する場合は、この方法を使用して認証を実行する必要があります。 

> [!WARNING]
> 機密情報を含む接続文字列とクエリは、すべての Kusto トレースから除外されるように難読化する必要があります。 詳細については、「 [難読化文字列リテラル](../query/scalar-data-types/string.md#obfuscated-string-literals)」を参照してください。

## <a name="optional-properties"></a>省略可能なプロパティ

| プロパティ            | Type            | 説明                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | テーブルのフォルダー。                  |
| `docString`         | `string`        | テーブルを文書化する文字列。      |
| `firetriggers`      | `true`/`false`  | `true`の場合、SQL テーブルで定義された挿入トリガーを起動するように対象システムに指示します。 既定値は、`false` です。 (詳細については、「 [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) と [SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx))」を参照してください。 |
| `createifnotexists` | `true`/ `false` | `true`の場合、対象の SQL テーブルが存在しない場合は作成されます。 `primarykey` この場合、主キーである結果列を示すために、このプロパティを指定する必要があります。 既定値は、`false` です。  |
| `primarykey`        | `string`        | がの場合 `createifnotexists` `true` 、このコマンドによって作成された場合、結果の列名が SQL テーブルの主キーとして使用されます。                  |

> [!NOTE]
> * テーブルが存在する場合、 `.create` コマンドは失敗し、エラーが表示されます。 `.create-or-alter`既存の `.alter` テーブルを変更するには、またはを使用します。 
> * 外部 SQL テーブルのスキーマまたは形式の変更はサポートされていません。 

に対する [データベースユーザー権限](../management/access-control/role-based-authorization.md) `.create` と、の [table admin 権限](../management/access-control/role-based-authorization.md) が必要です `.alter` 。 
 
**例** 

```kusto
.create external table MySqlExternalTable (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**出力**

| TableName   | TableType | フォルダー         | DocString | プロパティ                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| MySqlExternalTable | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable" ",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Server = tcp: database. windows. net, 1433;Authentication = Active Directory Integrated、Initial Catalog = mydatabase; "、<br>  "FireTriggers": true、<br>  "CreateIfNotExists": true、<br>  "PrimaryKey": "x"<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>SQL 型の外部テーブルのクエリ

外部 SQL テーブルのクエリはサポートされています。 「 [外部テーブルのクエリ](../../data-lake-query-data.md)」を参照してください。 

> [!Note]
> SQL 外部テーブルのクエリ実装では `SELECT x, s FROM MySqlTable` 、ステートメントを実行し `x` ます。ここで、と `s` は外部テーブルの列名です。 クエリの残りの部分は、Kusto 側で実行されます。

次の外部テーブルクエリについて考えてみます。 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto は、 `SELECT x, s FROM MySqlTable` SQL データベースに対してクエリを実行し、その後に kusto 側のカウントを実行します。 このような場合は、 `SELECT COUNT(1) FROM MySqlTable` 外部テーブル関数を使用するのではなく、t-sql で直接記述し、 [sql_request プラグイン](../query/sqlrequestplugin.md)を使用して実行すると、パフォーマンスが向上します。 同様に、フィルターは SQL クエリにプッシュされません。  

クエリが Kusto 側でさらに実行するためにテーブル全体 (または関連する列) の読み取りを必要とする場合は、外部テーブルを使用して SQL テーブルに対してクエリを実行します。 T-sql で SQL クエリを最適化できる場合は、 [sql_request プラグイン](../query/sqlrequestplugin.md)を使用します。

## <a name="next-steps"></a>次のステップ

* [外部テーブル全般制御コマンド](externaltables.md)
* [Azure Storage または Azure Data Lake の外部テーブルを作成および変更する](external-tables-azurestorage-azuredatalake.md)
