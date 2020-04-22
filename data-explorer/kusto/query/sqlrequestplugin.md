---
title: sql_requestプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーsql_requestプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f0c0837c6bb8e4dcd3cf2e28af18d02c19edb676
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766180"
---
# <a name="sql_request-plugin"></a>sql_request プラグイン

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *SqlQuery* *SqlParameters* `,` *Options* *ConnectionString* [Sql`,`パラメータ ] [オプション] `(``)`

プラグイン`sql_request`は SQL Server ネットワーク エンドポイントに SQL クエリを送信し、結果の最初の行セットを返します。

**引数**

* *接続文字列*: `string` SQL Server ネットワーク エンドポイントを指す接続文字列を示すリテラル。 認証の有効な方法とネットワーク エンドポイントを指定する方法については、以下を参照してください。

* *SqlQuery*: `string` SQL エンドポイントに対して実行されるクエリを示すリテラル。 1 つ以上の行セットを返す必要がありますが、最初の行セットのみが Kusto クエリの残りの部分で使用できます。

* *SqlParameters*: クエリと共`dynamic`にパラメーターとして渡すキーと値のペアを保持する型の定数値。 省略可能。
  
* *オプション*: キーと値`dynamic`のペアとして、より高度な設定を保持する型の定数値。 現在、`token`認証のために SQL エンドポイントに転送される呼び出し元が提供する AAD アクセス トークンを渡す場合にのみ設定できます。 省略可能。

**使用例**

次の例では、Azure SQL DB データベースに SQL クエリを`[dbo].[Table]`送信し、すべてのレコードを から取得し、Kusto 側で結果を処理します。 認証では、呼び出し元のユーザーの AAD トークンが再利用されます。

注: この例は、この方法でデータをフィルター処理/プロジェクトに推奨する方法として取るべきではありません。 Kusto オプティマイザは Kusto と SQL の間のクエリを最適化しようとしない現在、可能な限り最小のデータ セットを返すように SQL クエリを構築することが通常望ましいです。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

次の例は、SQL 認証がユーザー名/パスワードで行われる点を除いて、前の例と同じです。 機密性のために、ここでは難読化された文字列を使用しています。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

**認証**

sql_request プラグインは、SQL Server エンドポイントに対する認証の 3 つの方法をサポートしています。

* **AAD 統合認証**(`Authentication="Active Directory Integrated"`): これは、ユーザーまたはアプリケーションが AAD から Kusto を介して認証し、同じトークンを使用して SQL Server ネットワーク エンドポイントにアクセスする、優先される方法です。

* **ユーザー名/パスワード認証**(`User ID=...; Password=...;`): この方法のサポートは、AAD 統合認証を実行できない場合に提供されます。 秘密情報は Kusto を通じて送信されるので、可能な場合はこの方法を避けてください。

* **AAD アクセス**`dynamic({'token': h"eyJ0..."})`トークン ( ): この認証方法では、アクセス トークンは呼び出し元によって生成され、Kusto によって SQL エンドポイントに転送されます。 接続文字列には、 、 、`Authentication``User ID`または`Password`などの認証情報を含める必要があります。 代わりに、アクセストークンはsql_requestプラグイン`token`の引数の`Options`プロパティとして渡されます。
     
> [!WARNING]
> 接続文字列と機密情報や保護する必要がある情報を含むクエリは、Kusto トレースから除外されるように難読化する必要があります。
> 詳細については、[難読化された文字列リテラル](scalar-data-types/string.md#obfuscated-string-literals)を参照してください。

**暗号化とサーバーの検証**

セキュリティ上の理由から、SQL Server ネットワーク エンドポイントに接続する場合、次の接続プロパティが強制的に設定されます。

* `Encrypt`は無条件`true`に設定されます。
* `TrustServerCertificate`は無条件`false`に設定されます。

その結果、SQL Server は有効な SSL/TLS サーバー証明書を使用して構成する必要があります。

**ネットワーク エンドポイントの指定**

接続文字列の一部として SQL ネットワーク エンドポイントを指定する必要があります。
適切な構文は次のとおりです。

`Server``=` *FQDN* `,` *Port*FQDN [ ポート ] `tcp:`

各値の説明:

* *FQDN*は、エンドポイントの完全修飾ドメイン名です。

* *ポート*は、エンドポイントの TCP ポートです。 デフォルトでは、`1433`想定されます。

> [!NOTE]
> ネットワーク エンドポイントを指定するその他の形式はサポートされていません。
> たとえば、プログラムで SQL クライアント`tcp:`ライブラリを使用する場合でも、プレフィックスを省略することはできません。



::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
