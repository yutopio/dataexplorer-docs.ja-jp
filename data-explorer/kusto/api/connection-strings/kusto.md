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
ms.openlocfilehash: 8c5ade644f383a5a0d9e846b1a3143027d1eb467
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863185"
---
# <a name="kusto-connection-strings"></a>Kusto の接続文字列

Kusto 接続文字列は、kusto クライアントアプリケーションが Kusto サービスエンドポイントへの接続を確立するために必要な情報を提供します。 Kusto 接続文字列は、ADO.NET 接続文字列の後にモデル化されます。 つまり、接続文字列は、名前と値のパラメーターのペアをセミコロンで区切ったリストで、オプションで単一の URI をプレフィックスとして指定します。

**例:**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

URI は、と通信するためのサービスエンドポイントを提供します。

* ( `https://help.kusto.windows.net` )-プロパティの値 `Data Source` 。
* `Samples`(既定のデータベース)-プロパティの値 `Initial Catalog` 。

名前/値の構文を使用して、2つの追加のプロパティを指定します。 

* `Fed`プロパティ (とも呼ばれ `AAD Federated Security` ます) をに設定 `true` します。
* `Accept`プロパティがに設定さ `true` れています。

> [!NOTE]
>
> * プロパティ名は大文字と小文字が区別されず、名前と値のペアの間のスペースは無視されます。
> * プロパティ値では大文字と小文字**が区別され**ます。 セミコロン ( `;` )、単一引用符 ( `'` )、または二重引用符 () を含むプロパティ値は、 `"` 二重引用符で囲む必要があります。

いくつかの kusto クライアントツールでは、接続文字列の URI プレフィックスに対する拡張をサポートしています。これにより、 `@` _ClusterName_ `/` _InitialCatalog_を使用できるようになります。
たとえば、接続文字列は、 `@help/Samples` これらのツールによってに変換され `https://help.kusto.windows.net/Samples; Fed=true` ます。これは、3つのプロパティ ( `Data Source` 、 `Initial Catalog` 、および) を示し `AAD Federated Security` ます。

プログラムによって、Kusto 接続文字列を C# クラスで解析および操作でき `Kusto.Data.KustoConnectionStringBuilder` ます。 このクラスは、すべての接続文字列を検証し、検証に失敗した場合にランタイム例外を生成します。
この機能は、Kusto SDK のすべての種類に存在します。

## <a name="connection-string-properties"></a>接続文字列のプロパティ

次の表は、Kusto 接続文字列で指定できるすべてのプロパティを示しています。
プログラム名 (オブジェクト内のプロパティの名前 `Kusto.Data.KustoConnectionStringBuilder` ) とエイリアスである追加のプロパティ名の一覧が表示されます。

### <a name="general-properties"></a>全般プロパティ

| プロパティ名              | 代替名                      | プログラム名  | 説明                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| トレースのクライアントバージョン |                                        | TraceClientVersion | クライアントバージョンをトレースする場合は、この値を使用します。   |
| Data Source                | Addr、Address、Network Address、Server | DataSource         | Kusto サービスエンドポイントを指定する URI。 たとえば、`https://mycluster.kusto.windows.net` や `net.tcp://localhost` のようにします。               |
| Initial Catalog            | データベース                               | InitialCatalog     | 既定で使用されるデータベースの名前。 たとえば、MyDatabase のようになります。|
| クエリの一貫性          | QueryConsistency                       | QueryConsistency   | `strongconsistency` `weakconsistency` 実行前にクエリがメタデータと同期する必要があるかどうかを判断するには、をまたはに設定します。 |

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
|                                                  |                                          |EmbeddedManagedIdentity                       |マネージド id 認証で使用するアプリケーション id をクライアントに指示する文字列値。`system`システムによって割り当てられた id を示すには、を使用します。 このプロパティは、プログラムによってのみ接続文字列で設定することはできません。|ManagedServiceIdentity                        |TODO|
|アプリケーション証明書のサブジェクト識別名|アプリケーション証明書のサブジェクト           |ApplicationCertificateSubjectDistinguishedName||
|アプリケーション証明書発行者の識別名 |アプリケーション証明書の発行者            |ApplicationCertificateIssuerDistinguishedName ||
|アプリケーション証明書の送信公開証明書   |アプリケーション証明書 SendX5c、SendX5c  |ApplicationCertificateSendPublicCertificate   ||



### <a name="client-communication-properties"></a>クライアント通信のプロパティ

|プロパティ名                      |代替名|プログラム名  |説明                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|承諾      ||承諾      |エラー発生時に返される詳細なエラーオブジェクトを要求するブール値。|
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

**現在ログオンしているユーザー id を使用した AAD フェデレーション認証 (必要に応じてユーザーに表示されます)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;Authority Id={authority}"
```

**ユーザー id ヒントを使用した AAD フェデレーション認証 (必要に応じてユーザーに表示されます)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var userUPN = "johndoe@contoso.com";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);
kustoConnectionStringBuilder.UserID = userUPN;

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    UserID = userUPN,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;User ID={userUPN};Authority Id={authority}"
```

**ApplicationClientId と Applicationclientid を使用した AAD フェデレーションアプリケーション認証**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};Authority Id={authority}"
```

**ユーザー/アプリケーショントークンを使用した AAD フェデレーション認証**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var access_token = "<access token obtained from AAD>"

// Recommended syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadUserTokenAuthentication(access_token, authority);

// Legacy syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    UserToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source={serviceUri};Database=NetDefaultDB;Fed=True;UserToken={access_token};Authority Id={authority}"

// Recommended syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationTokenAuthentication(access_token, authority);

// Legacy syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppToken={applicationToken};Authority Id={authority}"
```

**トークンプロバイダーのコールバックを使用します (トークンが必要になるたびに呼び出されます)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
Func<string> tokenProviderCallback; // User-defined method to retrieve the access token

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadTokenProviderAuthentication(tokenProviderCallback);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    TokenProviderCallback = () => Task.FromResult(tokenProviderCallback()),
};
```

**マネージド Id の使用**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var managedIdentity = "<managed identity>"; // For system-assigned identity use "system"

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadManagedIdentity(managedIdentity);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    EmbeddedManagedIdentity = managedIdentity,
};
```

**X.509 証明書の使用**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
X509Certificate2 applicationCertificate = "<certificate blob>";
bool sendX5c = <desired value>; // Set too 'True' to use Trusted Issuer feature of AAD

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationCertificateAuthentication(applicationClientId, applicationCertificate, authority, sendX5c);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateBlob = applicationCertificate,
    ApplicationCertificateSendX5c = sendX5c,
    Authority = authority,
};
```

**拇印による x.509 証明書の使用 (クライアントはローカルストアから証明書を読み込もうとします)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};Authority Id={authority}"
```
