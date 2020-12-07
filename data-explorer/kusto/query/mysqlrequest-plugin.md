---
title: mysql_request プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの mysql_request プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e20e266e6fbae55c308cf13b7601277b8b0f30b2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320742"
---
# <a name="mysql_request-plugin-preview"></a>mysql_request プラグイン (プレビュー)

::: zone pivot="azuredataexplorer"

この `mysql_request` プラグインは、MySQL Server ネットワークエンドポイントに SQL クエリを送信し、結果の最初の行セットを返します。 クエリでは、複数の行セットが返される場合がありますが、Kusto クエリの残りの部分で使用できるのは最初の行セットだけです。

> [!IMPORTANT]
> `mysql_request`プラグインはプレビューモードであり、既定では無効になっています。
> プラグインを有効にするには、 [ `.enable plugin mysql_request` コマンド](../management/enable-plugin.md)を実行します。 有効になっているプラグインを確認するには、 [ `.show plugin` 管理コマンド](../management/show-plugins.md)を使用します。

## <a name="syntax"></a>構文

`evaluate``mysql_request` `(` *ConnectionString* `,` *sqlquery* [ `,` *sqlquery*]`)`

## <a name="arguments"></a>引数

名前 | 種類 | 説明 | 必須/省略可能 |
---|---|---|---
| *文字列* | `string` literal | MySQL サーバーネットワークエンドポイントを指す接続文字列を示します。 「 [認証](#username-and-password-authentication) 」および「 [ネットワークエンドポイント](#specify-the-network-endpoint)の指定方法」を参照してください。 | 必須 |
| *SqlQuery* | `string` literal | SQL エンドポイントに対して実行されるクエリを示します。 1つ以上の行セットを返す必要がありますが、Kusto クエリの残りの部分では最初の行セットのみを使用できます。 | 必須|
| *SqlParameters* | 型の定数値 `dynamic` | クエリと共にパラメーターとして渡すキーと値のペアを保持します。 | オプション |

## <a name="set-callout-policy"></a>コールアウトポリシーの設定

プラグインによって、MySql DB へのコールアウトが行われます。 クラスターの [コールアウトポリシー](../management/calloutpolicy.md) で `mysql` 、ターゲット *MySqlDbUri* への型の呼び出しが有効になっていることを確認します。

次の例では、MySQL DB のコールアウトポリシーを定義する方法を示します。 コールアウトポリシーを特定のエンドポイント (、) に制限することをお勧めし `my_endpoint1` `my_endpoint2` ます。

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

次の例は、CalloutType に対する alter 吹き出し policy コマンドを示してい `mysql` *CalloutType* ます。

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>ユーザー名とパスワードの認証

Mysql_request プラグインは、MySQL サーバーエンドポイントに対するユーザー名とパスワードの認証のみをサポートし、Azure Active Directory 認証とは統合されません。 

ユーザー名とパスワードは、次のパラメーターを使用して接続文字列の一部として指定します。

`User ID=...; Password=...;`
    
> [!WARNING]
> 機密情報または保護された情報は、すべての Kusto トレースから除外されるように、接続文字列やクエリから難読化する必要があります。 詳細については、「 [難読化文字列リテラル](scalar-data-types/string.md#obfuscated-string-literals)」を参照してください。

## <a name="encryption-and-server-validation"></a>暗号化とサーバー検証

セキュリティ上の理由から、 `SslMode` `Required` MySQL サーバーのネットワークエンドポイントに接続する場合、は無条件にに設定されます。 そのため、SQL Server は、有効な SSL/TLS サーバー証明書を使用して構成する必要があります。

## <a name="specify-the-network-endpoint"></a>ネットワークエンドポイントの指定

接続文字列の一部として、SQL ネットワークエンドポイントを指定します。

**構文**：

`Server``=` *FQDN* [ `Port` `=` *ポート*]

各値の説明:

* *FQDN* は、エンドポイントの完全修飾ドメイン名です。
* *Port* は、エンドポイントの TCP ポートです。 既定で `3306` は、が想定されます。

## <a name="examples"></a>例


### <a name="sql-query-to-azure-mysql-db"></a>Azure MySQL DB への SQL クエリ

次の例では、Azure MySQL DB データベースに SQL クエリを送信します。 からすべてのレコードを取得 `[dbo].[Table]` し、結果を処理します。

> [!NOTE]
> この例は、この方法でデータをフィルター処理または射影するための推奨事項としては取得できません。 Kusto オプティマイザーが Kusto と SQL の間のクエリを最適化しようとしていないため、SQL クエリを構築して、可能な限り小さいデータセットを返す必要があります。

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>ユーザー名とパスワードを使用した SQL 認証

次の例は前の例と同じですが、SQL 認証はユーザー名とパスワードによって行われます。 機密性を確保するために、難読化した文字列を使用します。

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>変更される Azure SQL DB への SQL クエリ

次の例では、からすべてのレコードを取得し、 `[dbo].[Table]` 別の列を追加して、 `datetime` Kusto 側で結果を処理する AZURE sql DB データベースに sql クエリを送信します。
SQL クエリで使用する SQL パラメーター () を指定し `@param0` ます。

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
