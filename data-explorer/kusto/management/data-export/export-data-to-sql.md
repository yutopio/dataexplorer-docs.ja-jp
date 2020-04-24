---
title: SQL へのデータのエクスポート - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの SQL へのデータのエクスポートについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7deeb3868800f00a828eb09cc605e4d7e5227032
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521626"
---
# <a name="export-data-to-sql"></a>SQL へのデータのエクスポート

データを SQL にエクスポートすると、クエリを実行し、その結果を SQL データベース (Azure SQL Database サービスによってホストされる SQL データベースなど) のテーブルに送信できます。

**構文**

`.export`[`async` `to` `sql` ] *Sql テーブル名*`with` `(` *Sql 接続文字列*[*プロパティ名*`=`*プロパティ値*`,`..`)`] `<|` *クエリ*

各値の説明:
* *非同期*: コマンドは非同期モードで実行されます (オプション)。
* *テーブル名*データが挿入される SQL データベーステーブル名。
  インジェクション攻撃から保護するために、この名前は制限されています。
* *SqlConnectionString*は`string`、接続文字列の形式に`ADO.NET`従って、接続先の SQL エンドポイントとデータベースを記述するリテラルです。 セキュリティ上の理由から、接続文字列は制限されています。
* *プロパティ名*、*プロパティ値*は、名前 (識別子) と値 (文字列リテラル) のペアです。

プロパティ:

|名前               |値           |説明|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` または `false`|の`true`場合、SQL テーブルに定義された INSERT トリガを起動するようにターゲット システムに指示します。 既定では、 `false`です。 (詳細については、「[一括挿入](https://msdn.microsoft.com/library/ms188365.aspx)」および[「システム.データ.SqlClient.SqlBulkCopy」を参照してください](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)。|
|`createifnotexists`|`true` または `false`|の`true`場合、ターゲット SQL テーブルが存在しない場合は作成されます。この`primarykey`場合、主キーである結果列を示すために、プロパティを指定する必要があります。 既定では、 `false`です。|
|`primarykey`       |                 |の`createifnotexists`場合`true`は、このコマンドで SQL テーブルの主キーとして使用される結果の列の名前を示します。|
|`persistDetails`   |`bool`           |コマンドが結果を保持する必要があることを示します`async`(フラグを参照)。 デフォルトは非同期`true`実行ですが、呼び出し元が結果を必要としない場合はオフにできます)。 同期実行`false`ではデフォルトですが、有効にすることもできます。 |
|`token`            |`string`         |Kusto が認証のために SQL エンドポイントに転送する AAD アクセス トークン。 設定されている場合、SQL 接続文字列には、 、、`Authentication``User ID`または`Password`などの認証情報を含める必要があります。|

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

SQL データベースにデータをエクスポートする場合、いくつかの制限事項と制約事項があります。

1. Kusto はクラウド サービスであるため、接続文字列はクラウドからアクセス可能なデータベースを指している必要があります。 (特に、オンプレミスのデータベースはパブリック クラウドからアクセスできないので、そのデータベースにエクスポートできません)。

2. Kusto は、呼び出し元のプリンシパルが Azure アクティブ`aaduser=`ディレクトリ`aadapp=`プリンシパル (または) である場合に、Active Directory 統合認証をサポートします。
   また、接続文字列の一部として SQL データベースの資格情報を提供することもできます。 他の認証方法はサポートされていません。 SQL データベースに提示される ID は、常に Kusto サービス ID 自体ではなく、コマンドの呼び出し元から発行されます。

3. SQL データベースにターゲット表が存在する場合は、クエリ結果スキーマと一致する必要があります。 場合によっては (Azure SQL Database など)、テーブルに ID 列としてマークされた列が 1 つあることを意味します。

4. 大量のデータをエクスポートする場合、時間がかかる場合があります。 一括インポート時には、ターゲットの SQL テーブルを最小限のログ記録に設定することをお勧めします。
   [データの一括インポートとエクスポート>データベース機能> SQL Server データベース エンジン> ..](https://msdn.microsoft.com/library/ms190422.aspx)を参照してください。

5. データ エクスポートは SQL 一括コピーを使用して実行され、ターゲット SQL データベースにトランザクション保証はありません。 詳細については、「[トランザクションおよび一括コピー操作](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations)」を参照してください。

6. SQL テーブル名は、文字、数字、スペース、アンダースコア ()、ドット (`_``.`) 、およびハイフン (`-`) から成る名前に制限されています。

7. SQL 接続文字列は、次のように制限されます。 `Persist Security Info` `false` `Encrypt` `true` `Trust Server Certificate` `false`

8. 新しい SQL テーブルを作成するときに、列の主キー プロパティを指定できます。 列が type`string`の場合、主キー列に関する他の制限により、SQL はテーブルの作成を拒否する可能性があります。 この問題を回避するには、データをエクスポートする前に、SQL でテーブルを手動で作成します。 この制限の理由は、SQL の主キー列のサイズは無制限にすることはできませんが、Kusto テーブル列には宣言されたサイズ制限がないためです。

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD 統合認証ドキュメント

* [SQL データベースでの認証に Azure アクティブ ディレクトリ認証を使用する](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
* [Azure SQL DB および SQL DW ツールの Azure AD 認証拡張機能](https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**使用例** 

この例では、Kusto はクエリを実行し、クエリによって生成された最初のレコード セット`MySqlTable`を server`MyDatabase``myserver`のデータベースのテーブルにエクスポートします。

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

この例では、Kusto はクエリを実行し、クエリによって生成された最初のレコード セット`MySqlTable`を server`MyDatabase``myserver`のデータベースのテーブルにエクスポートします。
ターゲット・データベースにターゲット表が存在しない場合は、作成されます。

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```