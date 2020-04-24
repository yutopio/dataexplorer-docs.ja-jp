---
title: Kusto 接続文字列-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Kusto 接続文字列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: bf31d8573266de1217ce93944a357c716d3ba508
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108169"
---
# <a name="kusto-connection-strings"></a>Kusto の接続文字列

Kusto 接続文字列は、kusto クライアントアプリケーションが Kusto サービスエンドポイントへの接続を確立するために必要な情報を提供します。 Kusto 接続文字列は、ADO.NET 接続文字列の後にモデル化されます。 つまり、接続文字列は、名前と値のパラメーターのペアをセミコロンで区切ったリストで、オプションで単一の URI をプレフィックスとして指定します。

**例:**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

URI は、と通信するためのサービスエンドポイントを提供します。

* (`https://help.kusto.windows.net`)- `Data Source`プロパティの値。
* `Samples`(既定のデータベース)-`Initial Catalog`プロパティの値。

名前/値の構文を使用して、2つの追加のプロパティを指定します。 

* `Fed`プロパティ (とも`AAD Federated Security`呼ばれます`true`) をに設定します。
* `Accept`プロパティがに`true`設定されています。

> [!NOTE]
>
> * プロパティ名は大文字と小文字が区別されず、名前と値のペアの間のスペースは無視されます。
> * プロパティ値では大文字と小文字**が区別され**ます。 セミコロン (`;`)、単一引用符 (`'`)、または二重引用符 (`"`) を含むプロパティ値は、二重引用符で囲む必要があります。

いくつかの kusto クライアントツールでは、接続文字列の URI プレフィックスに対する拡張をサポートしています`@` 。これにより、 _ClusterName_ `/` _InitialCatalog_を使用できるようになります。
たとえば`@help/Samples` 、接続文字列は、これらのツールによって`https://help.kusto.windows.net/Samples; Fed=true`に変換されます。`Data Source`これ`Initial Catalog`は、 `AAD Federated Security`3 つのプロパティ (、、および) を示します。

プログラムによって、Kusto 接続文字列を C# `Kusto.Data.KustoConnectionStringBuilder`クラスで解析および操作できます。 このクラスは、すべての接続文字列を検証し、検証に失敗した場合にランタイム例外を生成します。
この機能は、Kusto SDK のすべての種類に存在します。

## <a name="connection-string-properties"></a>接続文字列のプロパティ

次の表は、Kusto 接続文字列で指定できるすべてのプロパティを示しています。
プログラム名 ( `Kusto.Data.KustoConnectionStringBuilder`オブジェクト内のプロパティの名前) とエイリアスである追加のプロパティ名の一覧が表示されます。

### <a name="general-properties"></a>全般プロパティ

| プロパティ名              | 代替名                      | プログラム名  | 説明                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| トレースのクライアントバージョン |                                        | TraceClientVersion | クライアントバージョンをトレースする場合は、この値を使用します。   |
| Data Source                | Addr、Address、Network Address、Server | DataSource         | Kusto サービスエンドポイントを指定する URI。 たとえば、`https://mycluster.kusto.windows.net` や `net.tcp://localhost` のようにします。               |
| Initial Catalog            | データベース                               | InitialCatalog     | 既定で使用されるデータベースの名前。 たとえば、MyDatabase のようになります。|
| クエリの一貫性          | QueryConsistency                       | QueryConsistency   | 実行前に`strongconsistency`クエリ`weakconsistency`がメタデータと同期する必要があるかどうかを判断するには、をまたはに設定します。 |

### <a name="user-authentication-properties"></a>ユーザー認証のプロパティ

| プロパティ名          | 代替名                          | プログラム名 | 説明                       |
|------------------------|--------------------------------------------|-------------------|-----------------------------------|
| AAD フェデレーションセキュリティ | フェデレーションセキュリティ, フェデレーション, フィード, AADFed | FederatedSecurity | Azure Active を実行するようにクライアントに指示するブール値  |
| MFA を適用する            | MFA、EnforceMFA                             | EnforceMfa        | 多要素認証トークンを取得するようにクライアントに指示するブール値       |
| User ID                | UID、User                                  | UserID            | 指定されたユーザー名を使用してユーザー認証を実行するようにクライアントに指示する文字列値。           |
| トレースのユーザー名  |                                            | TraceUserName     | 要求を内部でトレースするときに使用するユーザー名をサービスに報告する文字列値         |
| ユーザートークン             | UsrToken、UserToken                        | UserToken         | 指定されたベアラートークンを使用してユーザー認証を実行するようにクライアントに指示する文字列値。<br/>ApplicationClientId、Applicationclientid、および Applicationclientid をオーバーライドします。 (指定されている場合は、指定されたトークンを優先して、実際のクライアント認証フローをスキップします。)       |
| 名前空間              | NS                                         | 名前空間         | (将来使用するため)  |



### <a name="application-authentication-properties"></a>アプリケーション認証のプロパティ

|プロパティ名                                     |代替名                         |プログラム名                             |説明      |
|--------------------------------------------------|------------------------------------------|----------------------------------------------|-----------------|
|AAD フェデレーションセキュリティ                            |フェデレーションセキュリティ, フェデレーション, フィード, AADFed|FederatedSecurity                             |Azure Active Directory (AAD) フェデレーション認証を実行するようにクライアントに指示するブール値|
|アプリケーション証明書の拇印                |Appcert.exe                                   |ApplicationCertificateThumbprint              |アプリケーションクライアント証明書の認証フローを使用するときに使用するクライアント証明書の拇印を提供する文字列値。|
|アプリケーションクライアント Id                             |AppClientId                               |ApplicationClientId                           |認証時に使用するアプリケーションクライアント ID を提供する文字列値。|
|アプリケーション キー                                   |AppKey                                    |ApplicationKey                                |アプリケーションシークレットフローを使用して認証するときに使用するアプリケーションキーを提供する文字列値|
|トレースのアプリケーション名                      |TraceAppName                              |ApplicationNameForTracing                     |要求を内部でトレースするときに使用するアプリケーション名をサービスに報告する文字列値|
|アプリケーショントークン                                 |AppToken                                  |ApplicationToken                              |指定されたベアラートークンを使用してアプリケーションの認証を実行するようにクライアントに指示する文字列値。|
|機関 Id                                      |TenantId                                  |Authority                                     |アプリケーションが登録されているテナントの名前または ID を示す文字列値|
|                                                  |                                          |EmbeddedManagedIdentity                       |マネージド id 認証で使用するアプリケーション id をクライアントに指示する文字列値。システム`system`によって割り当てられた id を示すには、を使用します。 このプロパティは、プログラムによってのみ接続文字列で設定することはできません。|ManagedServiceIdentity                        |TODO|
|アプリケーション証明書のサブジェクト識別名|アプリケーション証明書のサブジェクト           |ApplicationCertificateSubjectDistinguishedName||
|アプリケーション証明書発行者の識別名 |アプリケーション証明書の発行者            |ApplicationCertificateIssuerDistinguishedName ||
|アプリケーション証明書の送信公開証明書   |アプリケーション証明書 SendX5c、SendX5c  |ApplicationCertificateSendPublicCertificate   ||



### <a name="client-communication-properties"></a>クライアント通信のプロパティ

|プロパティ名                      |代替名|プログラム名  |説明                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|Accept      ||Accept      |エラー発生時に返される詳細なエラーオブジェクトを要求するブール値。|
|ストリーミング   ||ストリーミング   |クライアントがデータを蓄積せずに呼び出し元に提供することを要求するブール値。|
|非圧縮||非圧縮|クライアントがトランスポートレベルの圧縮を要求しないことを要求するブール値。|

## <a name="authentication-properties-details"></a>認証のプロパティ (詳細)

接続文字列の重要なタスクの1つは、サービスに対する認証方法をクライアントに指示することです。
次のアルゴリズムは、一般に、HTTP/HTTPS エンドポイントに対する認証のためにクライアントによって使用されます。

1. AadFederatedSecurity が true の場合:
    1. UserToken が指定されている場合は、指定されたトークンで AAD フェデレーション認証を使用します
    1. それ以外の場合、ApplicationToken が指定されている場合は、指定されたトークンでフェデレーション認証を実行します。
    1. それ以外の場合、ApplicationClientId と Applicationclientid が指定されている場合は、指定されたアプリケーションクライアント ID とキーでフェデレーション認証を実行します。
    1. それ以外の場合、ApplicationClientId と Applicationclientid が指定されている場合は、指定されたアプリケーションクライアント ID と証明書でフェデレーション認証を実行します。
    1. それ以外の場合は、現在ログオンしているユーザーの id でフェデレーション認証を実行します (セッションの最初の認証であるかどうかを確認するメッセージが表示されます)。

1. それ以外の場合は認証しません。





### <a name="aad-federated-application-authentication-with-application-certificate"></a>アプリケーション証明書を使用した AAD フェデレーションアプリケーション認証

1. アプリケーションの証明書に基づく認証は、(ネイティブクライアントアプリケーションではなく) web アプリケーションでのみサポートされています。
1. Web アプリケーションは、指定された証明書を受け入れるように構成する必要があります。 [AAD アプリケーションの証明書ベースの認証方法](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
1. Web アプリケーションは、関連する Kusto クラスター内で承認されたプリンシパルとして構成する必要があります。
1. 指定された拇印の証明書がインストールされている必要があります (ローカルコンピューターストアまたは現在のユーザーストア内)。
1. 証明書の公開キーには、少なくとも2048ビットが含まれている必要があります。

## <a name="aad-based-authentication-examples"></a>AAD ベースの認証の例

**現在ログオンしているユーザーの id に基づく AAD フェデレーション認証 (必要に応じてユーザーに表示されます)**

```csharp
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
  .WithAadUserPromptAuthentication(authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;authority={authority}"
```

**指定された ApplicationClientId および Applicationclientid に基づく AAD フェデレーションアプリケーション認証**

```csharp
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = APP_GUID;
var applicationKey = secret;
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};authority={authority}"
```

**特定のユーザーの/アプリケーションのトークンに基づく AAD フェデレーション認証**

```csharp
var serviceNameAndRegion = "help";
var databaseName = "NetDefaultDB";
var clusterAndDatabase = string.Format(
    "https://{0}.kusto.windows.net/{1}",
    serviceNameAndRegion, databaseName);

// AAD User - Option 1
var userToken = "<UserToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
    .WithAadUserTokenAuthentication(userToken);

// AAD User - Option 2
var userToken = "<UserToken>";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
{
    FederatedSecurity = true,
    UserToken = userToken,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;UserToken={user_token};authority={authority}"

// AAD Application - Option 1
var applicationToken = "<ApplicationToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
    .WithAadApplicationTokenAuthentication();

// AAD Application - Option 2
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationToken = "<UserToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
{
    FederatedSecurity = true,
    ApplicationToken = applicationToken,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppToken={applicationToken};authority={authority}"
```

**証明書の拇印を使用している (クライアントはローカルストアから証明書を読み込もうとします)**

```csharp
var serviceNameAndRegion = "help";
var databaseName = "Samples";
var clusterAndDatabase = string.Format(
    "https://{0}.kusto.windows.net/{1}",
    serviceNameAndRegion, databaseName);

string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Option 2
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(clusterAndDatabase)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};authority={authority}"
```

