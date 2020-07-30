---
title: sql_request プラグイン-Azure データエクスプローラー
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
ms.openlocfilehash: e412c1ec4f08af9820018f4c8dc172bd8c748a7f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350980"
---
# <a name="sql_request-plugin"></a>sql_request プラグイン

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `(` *ConnectionString* `,` *sqlquery* [ `,` *sqlquery* [ `,` *オプション*]]`)`

プラグインは、 `sql_request` SQL Server ネットワークエンドポイントに SQL クエリを送信し、結果の最初の行セットを返します。

## <a name="arguments"></a>引数

* *ConnectionString*: `string` SQL Server ネットワークエンドポイントを指す接続文字列を示すリテラルです。 [認証の有効な方法](#authentication)と、[ネットワークエンドポイント](#specify-the-network-endpoint)を指定する方法を参照してください。

* *Sqlquery*: `string` SQL エンドポイントに対して実行されるクエリを示すリテラル。 1つ以上の行セットを返す必要がありますが、Kusto クエリの残りの部分では最初の行セットのみを使用できます。

* *Sqlparameters*: `dynamic` クエリと共にパラメーターとして渡すキーと値のペアを保持する型の定数値。 省略可能。
  
* *Options*: `dynamic` キーと値のペアとしてより高度な設定を保持する型の定数値。 現時点では、 `token` 認証のために SQL エンドポイントに転送される呼び出し元提供の Azure AD アクセストークンを渡すためにのみ、設定できます。 省略可能。

## <a name="examples"></a>例

次の例では、Azure SQL DB データベースに SQL クエリを送信します。 からすべてのレコードを取得 `[dbo].[Table]` し、Kusto 側の結果を処理します。 認証では、呼び出し元のユーザーの Azure AD トークンを再利用します。 

> [!NOTE]
> この例は、この方法でデータをフィルター処理または射影するための推奨事項としては使用できません。 Kusto オプティマイザーが Kusto と SQL の間のクエリを最適化しようとしていないため、SQL クエリを構築して、可能な限り小さいデータセットを返す必要があります。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

次の例は、前の例と同じですが、ユーザー名/パスワードによって SQL 認証が行われる点が異なります。 機密性を確保するために、ここでは難読化した文字列を使用します。

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

次の例では、からすべてのレコードを取得し、 `[dbo].[Table]` 別の列を追加して、 `datetime` Kusto 側で結果を処理する AZURE sql DB データベースに sql クエリを送信します。
SQL クエリで使用する SQL パラメーター () を指定し `@param0` ます。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

## <a name="authentication"></a>認証

Sql_request プラグインは、SQL Server エンドポイントに対する3つの認証方法をサポートしています。

### <a name="azure-ad-integrated-authentication"></a>Azure AD 統合認証 

`Authentication="Active Directory Integrated"`

  Azure AD 統合認証が推奨される方法です。 このメソッドでは、ユーザーまたはアプリケーションが Kusto Azure AD 経由で認証されます。 その後、同じトークンを使用して SQL Server ネットワークエンドポイントにアクセスします。

### <a name="usernamepassword-authentication"></a>ユーザー名/パスワード認証

`User ID=...; Password=...;`

  Azure AD 統合認証を実行できない場合、ユーザー名とパスワードの認証がサポートされます。 シークレット情報は Kusto に送信されるため、可能な場合はこの方法を使用しないようにしてください。

### <a name="azure-ad-access-token"></a>Azure AD アクセストークン

`dynamic({'token': h"eyJ0..."})`

   Azure AD アクセストークンの認証方法を使用すると、呼び出し元は、Kusto から SQL エンドポイントに転送されるアクセストークンを生成します。 接続文字列には `Authentication` 、、、またはのような認証情報を含めないで `User ID` `Password` ください。 代わりに、アクセストークンは、 `token` sql_request プラグインの引数にプロパティとして渡され `Options` ます。
     
> [!WARNING]
> 機密情報や保護が必要な情報を含む接続文字列とクエリは、すべての Kusto トレースから除外するように難読化する必要があります。
> 詳細については、「[難読化文字列リテラル](scalar-data-types/string.md#obfuscated-string-literals)」を参照してください。

## <a name="encryption-and-server-validation"></a>暗号化とサーバー検証

セキュリティ上の理由により、SQL Server ネットワークエンドポイントに接続するときは、次の接続プロパティが強制されます。

* `Encrypt`は無条件に設定され `true` ます。
* `TrustServerCertificate`は無条件に設定され `false` ます。

そのため、SQL Server は、有効な SSL/TLS サーバー証明書を使用して構成する必要があります。

## <a name="specify-the-network-endpoint"></a>ネットワークエンドポイントの指定

接続文字列の一部として SQL ネットワークエンドポイントを指定することは必須です。
適切な構文は次のとおりです。

`Server``=` `tcp:` *FQDN* [ `,` *ポート*]

この場合、

* *FQDN*は、エンドポイントの完全修飾ドメイン名です。
* *Port*は、エンドポイントの TCP ポートです。 既定で `1433` は、が想定されます。

> [!NOTE]
> ネットワークエンドポイントを指定するその他の形式はサポートされていません。
> たとえば、 `tcp:` プログラムで SQL クライアントライブラリを使用するときに、プレフィックスを省略することはできません。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
