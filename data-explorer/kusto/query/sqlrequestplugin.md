---
title: sql_request プラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの sql_request プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 725021ad8089d7e9ad4f897bd5a1c68f6912bf7a
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737285"
---
# <a name="sql_request-plugin"></a>sql_request プラグイン

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *Options* *ConnectionString* `,` *SqlParameters* *SqlQuery* ConnectionString sqlquery [sqlquery [オプション]] `,` `(``)`

プラグイン`sql_request`は、SQL Server ネットワークエンドポイントに SQL クエリを送信し、結果の最初の行セットを返します。

**引数**

* *ConnectionString*: SQL Server `string`ネットワークエンドポイントを指す接続文字列を示すリテラルです。 認証の有効な方法と、ネットワークエンドポイントを指定する方法については、以下を参照してください。

* *Sqlquery*: SQL `string`エンドポイントに対して実行されるクエリを示すリテラル。 1つ以上の行セットを返す必要がありますが、Kusto クエリの残りの部分では最初の行セットのみを使用できます。

* *Sqlparameters*: クエリと共にパラメーター `dynamic`として渡すキーと値のペアを保持する型の定数値。 任意。
  
* *Options*: キーと値のペア`dynamic`としてより高度な設定を保持する型の定数値。 現在、 `token`設定できるのは、認証のために SQL エンドポイントに転送される呼び出し元が提供する AAD アクセストークンを渡すことだけです。 任意。

**使用例**

次の例では、からすべての`[dbo].[Table]`レコードを取得する AZURE sql DB データベースに sql クエリを送信し、Kusto 側で結果を処理します。 認証では、呼び出し元のユーザーの AAD トークンを再利用します。

注: この方法では、データをフィルター処理または射影するための推奨事項としてこの例を使用することはできません。 通常、Kusto オプティマイザーは Kusto と SQL 間のクエリを最適化しようとしないので、SQL クエリは可能な限り最小のデータセットを返すように構築することをお勧めします。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

次の例は、前の例と同じですが、ユーザー名/パスワードによって SQL 認証が行われる点が異なります。 機密性を確保するために、ここでは難読化された文字列を使用します。

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

Sql_request プラグインは、SQL Server エンドポイントに対する3つの認証方法をサポートしています。

* **Aad 統合認証**(`Authentication="Active Directory Integrated"`): これは推奨される方法です。この方法では、ユーザーまたはアプリケーションが aad 経由で kusto に対して認証を行い、同じトークンを使用して SQL Server ネットワークエンドポイントにアクセスします。

* **ユーザー名/パスワード認証**(`User ID=...; Password=...;`): AAD 統合認証を実行できない場合は、この方法のサポートが提供されます。 シークレット情報は Kusto に送信されるため、可能な場合はこの方法を使用しないようにしてください。

* **AAD アクセストークン**(`dynamic({'token': h"eyJ0..."})`): この認証方法では、アクセストークンが呼び出し元によって生成され、kusto によって SQL エンドポイントに転送されます。 接続文字列には、、 `Authentication` `User ID`、または`Password`のような認証情報を含めないでください。 代わりに、アクセストークンは、sql_request プラグイン`token`の`Options`引数にプロパティとして渡されます。
     
> [!WARNING]
> 機密情報や保護が必要な情報を含む接続文字列とクエリは、すべての Kusto トレースから除外されるように難読化する必要があります。
> 詳細については、「[難読化文字列リテラル](scalar-data-types/string.md#obfuscated-string-literals)」を参照してください。

**暗号化とサーバー検証**

次の接続プロパティは、セキュリティ上の理由により、SQL Server ネットワークエンドポイントに接続するときに強制的に適用されます。

* `Encrypt`は無条件に`true`設定されます。
* `TrustServerCertificate`は無条件に`false`設定されます。

そのため、SQL Server は、有効な SSL/TLS サーバー証明書を使用して構成する必要があります。

**ネットワークエンドポイントの指定**

接続文字列の一部として SQL ネットワークエンドポイントを指定することは必須です。
適切な構文は次のとおりです。

`Server``=` `,` *Port*FQDN [ポート] *FQDN* `tcp:`

各値の説明:

* *FQDN*は、エンドポイントの完全修飾ドメイン名です。

* *Port*は、エンドポイントの TCP ポートです。 既定では`1433` 、が想定されます。

> [!NOTE]
> ネットワークエンドポイントを指定するその他の形式はサポートされていません。
> たとえば、プログラムで SQL クライアントライブラリを使用`tcp:`するときに、プレフィックスを省略することはできません。



::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
