---
title: SQL にデータをエクスポートする-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーで SQL にデータをエクスポートする方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61c65e2667356f2b2ee9e53c3dd1c210e84c72f8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342809"
---
# <a name="export-data-to-sql"></a>SQL にデータをエクスポートする

SQL にデータをエクスポートすると、クエリを実行し、その結果を SQL database のテーブル (Azure SQL Database サービスによってホストされる SQL データベースなど) に送信できます。

**構文**

`.export`[ `async` ] `to` `sql` *Sqltablename* *sqltablename* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] `<|`*クエリ*

各値の説明:
* *async*: コマンドを非同期モードで実行します (省略可能)。
* *Sqltablename* データが挿入される SQL データベーステーブルの名前。
  インジェクション攻撃から保護するために、この名前は制限されています。
* *Sqlconnectionstring* は、 `string` 接続文字列の形式に従ったリテラルで `ADO.NET` あり、接続先の SQL エンドポイントとデータベースについて説明します。 セキュリティ上の理由から、接続文字列は制限されています。
* *PropertyName*、 *PropertyValue* は、名前 (識別子) と値 (文字列リテラル) のペアです。

プロパティ:

|名前               |値           |説明|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` または `false`|`true`の場合、SQL テーブルで定義された挿入トリガーを起動するように対象システムに指示します。 既定値は、`false` です。 (詳細については、「 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) と [SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy))」を参照してください。|
|`createifnotexists`|`true` または `false`|`true`の場合、対象の SQL テーブルが存在しない場合は作成されます。 `primarykey` この場合は、主キーである結果列を示すためにプロパティを指定する必要があります。 既定値は、`false` です。|
|`primarykey`       |                 |がの場合 `createifnotexists` `true` 、このコマンドで SQL テーブルの主キーとして使用される、結果の列の名前を示します。|
|`persistDetails`   |`bool`           |コマンドが結果を永続化する必要があることを示します (フラグを参照 `async` )。 `true`非同期実行では、は既定でになりますが、呼び出し元が結果を必要としない場合は無効にすることができます。 `false`同期実行では、は既定でに設定されますが、有効にすることができます。 |
|`token`            |`string`         |Kusto が認証のために SQL エンドポイントに転送する AAD アクセストークン。 設定すると、SQL 接続文字列に、、またはのような認証情報を含めないで `Authentication` `User ID` `Password` ください。|

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

SQL database にデータをエクスポートする際には、いくつかの制限事項と制約事項があります。

1. Kusto はクラウドサービスなので、接続文字列はクラウドからアクセスできるデータベースを指す必要があります。 (特に、パブリッククラウドからアクセスできないため、オンプレミスのデータベースにエクスポートすることはできません)。

2. Kusto は、呼び出し元のプリンシパルが Azure Active Directory プリンシパル (または) の場合に Active Directory 統合認証をサポート `aaduser=` `aadapp=` します。
   また、Kusto では、接続文字列の一部として SQL データベースの資格情報を指定することもできます。 その他の認証方法はサポートされていません。 SQL database に提示される id は、Kusto サービス id 自体ではなく、コマンドの呼び出し元から常に長年ます。

3. SQL データベースの対象のテーブルが存在する場合は、クエリの結果スキーマと一致している必要があります。 場合によっては (Azure SQL Database など)、テーブルに1つの列が id 列としてマークされていることに注意してください。

4. 大量のデータをエクスポートすると、長い時間がかかる場合があります。 一括インポート中に最小ログ記録の対象となる SQL テーブルを設定することをお勧めします。
   [データの一括インポートと一括エクスポートについては、「SQL Server データベースエンジン >... > データベース > 機能](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import)」を参照してください。

5. データのエクスポートは、SQL 一括コピーを使用して実行され、対象の SQL データベースに対するトランザクションの保証は提供されません。 詳細については [、「トランザクションと一括コピーの操作](/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) 」を参照してください。

6. SQL テーブル名は、文字、数字、スペース、アンダースコア ( `_` )、ドット ( `.` )、およびハイフン () で構成される名前に制限されてい `-` ます。

7. SQL 接続文字列は次のように制限されます。 `Persist Security Info` が明示的にに設定され `false` 、 `Encrypt` がに設定され、 `true` `Trust Server Certificate` がに設定されてい `false` ます。

8. 新しい SQL テーブルを作成するときに、列の主キープロパティを指定できます。 列の型がである場合 `string` 、主キー列に対する他の制限により、SQL はテーブルの作成を拒否する可能性があります。 この回避策は、データをエクスポートする前に、SQL でテーブルを手動で作成することです。 この制限の理由は、SQL の主キー列のサイズを無制限にすることはできませんが、Kusto テーブルの列には、宣言されたサイズの制限がないためです。

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD 統合認証のドキュメント

* [SQL Database を使用した認証に Azure Active Directory 認証を使用する](/azure/sql-database/sql-database-aad-authentication)
* [Azure SQL DB および SQL DW tools 用の認証拡張機能の Azure AD](https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**使用例** 

この例では、Kusto はクエリを実行し、クエリによって生成された最初のレコードセットをサーバー内のデータベースのテーブルにエクスポートし `MySqlTable` `MyDatabase` `myserver` ます。

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

この例では、Kusto はクエリを実行し、クエリによって生成された最初のレコードセットをサーバー内のデータベースのテーブルにエクスポートし `MySqlTable` `MyDatabase` `myserver` ます。
ターゲットテーブルがターゲットデータベースに存在しない場合は、作成されます。

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```