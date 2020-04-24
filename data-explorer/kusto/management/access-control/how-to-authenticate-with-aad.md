---
title: Azure データ エクスプローラー アクセス用の AAD での認証方法 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Azure データ エクスプローラー へのアクセスに関する AAD での認証方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: dc3a87a290ac535ac475af5017170a0478cd9b54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522918"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Azure データ エクスプローラー アクセス用の AAD での認証方法

Azure データ エクスプローラーにアクセスする推奨される方法は **、Azure アクティブ ディレクトリ**サービス **(Azure AD**、または単に**AAD**とも呼ばれることもあります) に認証することです。 これにより、Azure Data Explorer が 2 段階のプロセスを使用してアクセス プリンシパルのディレクトリ資格情報を見ることは決してありません。

1. 最初の手順では、クライアントは AAD サービスと通信し、認証を行い、クライアントがアクセスしようとしている特定の Azure Data Explorer エンドポイントに対して特別に発行されたアクセス トークンを要求します。
2. 2 番目の手順では、クライアントが要求を Azure データ エクスプローラーに発行し、最初の手順で取得したアクセス トークンを ID の証明として Azure データ エクスプローラーに提供します。

Azure データ エクスプローラーは、AAD がアクセス トークンを発行したセキュリティ プリンシパルに代わって要求を実行し、すべての承認チェックがこの ID を使用して実行されます。

ほとんどの場合、Azure Data Explorer SDK の 1 つを使用してサービスにプログラムでアクセスすることを推奨します。 [.NET SDK](../../api/netfx/about-the-sdk.md)などのを参照してください。
認証プロパティは[、Kusto 接続文字列](../../api/connection-strings/kusto.md)によって設定されます。
それが不可能な場合は、このフローを自分で実装する方法の詳細については、こちらをご覧ください。

主な認証シナリオは次のとおりです。

* **サインインしているユーザーを認証するクライアント アプリケーション**。
  このシナリオでは、対話型 (クライアント) アプリケーションは、資格情報 (ユーザー名やパスワードなど) のユーザーに AAD プロンプトをトリガーします。
  ユーザー[認証](#user-authentication)を参照してください。

* **"ヘッドレス" アプリケーション**。
  このシナリオでは、アプリケーションは、資格情報を提供するユーザーが存在せずに実行され、代わりに、アプリケーションが構成されている資格情報を使用して AAD に対して "それ自体" として認証します。
  [アプリケーション認証](#application-authentication)を参照してください。

* **代理認証**:
  このシナリオでは、"Web サービス" または "Web アプリ" のシナリオと呼ばれる場合があり、アプリケーションは別のアプリケーションから AAD アクセス トークンを取得し、Azure Data Explorer で使用できる別の AAD アクセス トークンに変換します。
  つまり、アプリケーションは、資格情報を提供したユーザーまたはアプリケーションと Azure Data Explorer サービスとの間のメディエーターとして機能します。
  [「認証の代理](#on-behalf-of-authentication)」を参照してください。

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Azure データ エクスプローラーの AAD リソースの指定

AAD からアクセス トークンを取得する場合、クライアントはトークンを発行する**AAD リソースを AAD**に通知する必要があります。 Azure データ エクスプローラー エンドポイントの AAD リソースは、エンドポイントの URI であり、ポート情報とパスを禁止します。 次に例を示します。

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>AAD テナント ID の指定

AAD はマルチテナント サービスであり、すべての組織が AAD に**ディレクトリ**と呼ばれるオブジェクトを作成できます。 ディレクトリ オブジェクトは、ユーザー アカウント、アプリケーション、グループなどのセキュリティ関連オブジェクトを保持します。 AAD は、多くの場合、ディレクトリをテナントと呼**ばれます**。 AAD テナントは GUID (**テナント ID**) によって識別されます。 多くの場合、AAD テナントは組織のドメイン名で識別できます。

たとえば、"Contoso" という組織には、テナント ID`4da81d62-e0a8-4899-adad-4349ca6bfe24`とドメイン名`contoso.com`が含まれます。

## <a name="specifying-the-aad-authority"></a>AAD 権限の指定

AAD には、認証用のエンドポイントが多数含まれています。

* 認証対象のプリンシパルをホストしているテナントが既知の場合 (つまり、ユーザーまたはアプリケーションがどの AAD ディレクトリに存在するかを知っているとき)、AAD`https://login.microsoftonline.com/{tenantId}`エンドポイントは です。
  ここでは、AAD`{tenantId}`の組織のテナント ID またはドメイン名 (例: ) を`contoso.com`示します。

* 認証対象のプリンシパルをホストしているテナントが不明な場合、`{tenantId}`上記の値を置き換えることで「共通」エンドポイントを使用できます`common`。

> [!NOTE]
> 認証に使用される AAD エンドポイントは **、AAD 権限 URL**または単に**AAD 権限**とも呼ばれます。

## <a name="aad-token-cache"></a>AAD トークン キャッシュ

Azure データ エクスプローラー SDK を使用する場合、AAD トークンはユーザーごとのトークン キャッシュ内のローカル コンピューターに格納されます (サインインしているユーザーのみがアクセスまたは復号化できるファイルは **%APPDATA%\Kusto\tokenCache.data**と呼ばれます)。キャッシュは、ユーザーに資格情報の入力を求める前にトークンを検査するため、ユーザーが資格情報を入力する必要がある回数が大幅に減少します。

> [!NOTE]
> AAD トークン キャッシュは、ユーザーが Azure データ エクスプローラーにアクセスする対話型のプロンプトの数を減らしますが、完全なプロンプトは減少しません。 さらに、ユーザーは、資格情報の入力を求められる場合、事前に予測することはできません。
> つまり、非対話型ログオン (たとえば、タスクのスケジュール設定時など) をサポートする必要がある場合は、ユーザー アカウントを使用して Azure Data Explorer にアクセスしないでください。


## <a name="user-authentication"></a>ユーザー認証

ユーザー認証を使用して Azure データ エクスプローラーにアクセスする最も簡単な方法は、Azure`Federated Authentication`データ エクスプローラー SDK を使用`true`して、Azure データ エクスプローラー接続文字列のプロパティを に設定することです。 SDK を使用してサービスに要求を送信するときには、ユーザーに AAD 資格情報を入力するためのサインイン フォームが表示され、認証が成功すると要求が送信されます。

Azure データ エクスプローラー SDK を使用しないアプリケーションは、AAD サービス セキュリティ プロトコル クライアントを実装する代わりに AAD クライアント ライブラリ (ADAL) を使用できます。 .NEThttps://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNetアプリケーションから行う例については、 [ ] を参照してください。

Azure データ エクスプローラー アクセスのユーザーを認証するには、アプリケーションに委任`Access Kusto`されたアクセス許可を最初に付与する必要があります。 詳細については[、AAD アプリケーションのプロビジョニングに関する Kusto ガイド](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application)を参照してください。

次の簡単なコード スニペットは、ADAL を使用して Azure データ エクスプローラーにアクセスする AAD ユーザー トークンを取得する方法を示しています (ログオン UI を起動します)。

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire user token for the interactive user for Kusto:
AuthenticationResult result = authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", "your client app id", 
    new Uri("your client app resource id"), new PlatformParameters(PromptBehavior.Auto)).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="application-authentication"></a>アプリケーション認証

次の簡単なコード スニペットは、ADAL を使用して Azure データ エクスプローラーにアクセスするための AAD アプリケーション トークンを取得する方法を示しています。 このフローでは、プロンプトは表示されず、アプリケーションは AAD に登録され、アプリケーション認証を実行するために必要な資格情報 (AAD によって発行されたアプリ キー、または AAD に事前登録されている X509v2 証明書など) を備える必要があります。

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire application token for Kusto:
ClientCredential applicationCredentials = new ClientCredential("your application client ID", "your application key");
AuthenticationResult result =
        authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", applicationCredentials).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="on-behalf-of-authentication"></a>代理認証

このシナリオでは、アプリケーションがアプリケーションによって管理されている任意のリソースの AAD アクセス トークンを送信し、そのトークンを使用して、元の AAD アクセス トークンによって示されたプリンシパルに代わって Kusto にアクセスできるように、そのトークンを使用して Azure Data Explorer リソースの新しい AAD アクセス トークンを取得します。

このフローは[OAuth2 トークン交換フロー](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)と呼ばれます。
一般的に、AAD を使用して複数の構成手順を実行する必要があり、場合によっては (AAD テナントの構成によっては) AAD テナントの管理者からの特別な同意が必要になる場合があります。



**手順 1: アプリケーションと Azure データ エクスプローラー サービスとの間の信頼関係を確立する**

1. Azure[ポータル](https://portal.azure.com/)を開き、正しいテナントにサインインしていることを確認します (ポータルへのサインインに使用する ID については、右上隅を参照)。

2. [リソース] ウィンドウで **、[Azure Active Directory]** をクリックし、[**アプリの登録**] をクリックします。

3. 代理フローを使用するアプリケーションを見つけて開きます。

4. [API**のアクセス許可**] をクリックし **、[アクセス許可の追加]** をクリックします。

5. **Azure データ エクスプローラー**という名前のアプリケーションを検索し、選択します。

6. **[user_impersonation / アクセス Kusto**] を選択します。

7. [**アクセス許可の追加 ]** をクリックします。

**手順 2: サーバー コードでトークン交換を実行する**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for for Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application 
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**手順 3: Kusto クライアント ライブラリにトークンを提供し、クエリを実行する**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}", 
    clusterName, 
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>ウェブクライアント (JavaScript) 認証と承認



**AAD アプリケーションの構成**

> [!NOTE]
> AAD アプリをセットアップするために実行する必要がある標準[手順](./how-to-provision-aad-app.md)に加えて、AAD アプリケーションでの oauth の暗黙的なフローも有効にする必要があります。 そのためには、Azure ポータルのアプリケーション ページからマニフェストを選択し、oauth2AllowImplicitFlow を true に設定します。

**詳細**

クライアントがユーザーのブラウザーで実行されている JavaScript コードである場合、暗黙的な許可フローが使用されます。 クライアント アプリケーションに Azure Data Explorer サービスへのアクセスを許可するトークンは、リダイレクト URI の一部として認証が成功した直後に提供されます (URI フラグメント内)。このフローでは更新トークンが指定されないので、クライアントは長期間トークンをキャッシュして再利用することはできません。

ネイティブ クライアント フローと同様に、2 つの AAD アプリケーション (サーバーとクライアント) と、それらの間に構成済みのリレーションシップが存在する必要があります。 

AdalJs は、access_token呼び出しが行われる前にid_tokenを取得する必要があります。

アクセス トークンは`AuthenticationContext.login()`メソッドを呼び出すことによって取得され、access_tokensを呼`Authenticationcontext.acquireToken()`び出すことによって取得されます。

* 適切な構成で認証コンテキストを作成します。

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* ログイン`authContext.login()`していない`acquireToken()`場合は、お試しください。 あなたがログインしているかどうかを知る良い方法は、電話`authContext.getCachedUser()`してそれが戻`false`ってくるかどうかを確認する方法です)
* ページ`authContext.handleWindowCallback()`が読み込まれるたびに呼び出します。 これは、AAD からリダイレクトをインターセプトし、フラグメント URL からトークンを引き出してキャッシュするコードの一部です。
* 有効`authContext.acquireToken()`な ID トークンが得られる現在、実際のアクセス トークンを取得するために呼び出します。 トークンを取得する最初のパラメーターは、Kusto サーバー AAD アプリケーション リソース URL です。  

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* トークンを Azure データ エクスプローラー要求のベアラー トークンとして使用できます。 次に例を示します。

```javascript
var settings = {
    url: "https://" + clusterAndRegionName + ".kusto.windows.net/v1/rest/query",
    type: "POST",
    data: JSON.stringify({
        "db": dbName,
        "csl": query,
        "properties": null
    }),
    contentType: "application/json; charset=utf-8",
    headers: { "Authorization": "Bearer " + token },
    dataType: "json",
    jsonp: false,
    success: function(data, textStatus, jqXHR) {
        if (successCallback !== undefined) {
            successCallback(data.Tables[0]);
        }

    },
    error: function(jqXHR, textStatus, errorThrown) {
        if (failureCallback !==  undefined) {
            failureCallback(textStatus, errorThrown, jqXHR.responseText);
        }

    },
};

$.ajax(settings).then(function(data) {/* do something wil the data */});
``` 

> 警告 - 認証時に次のような例外が発生した場合:`ReferenceError: AuthenticationContext is not defined` 
おそらく、グローバル名前空間に認証コンテキストがないからです。 残念ながら、AdalJS は現在、認証コンテキストがグローバル名前空間で定義されるという文書化されていない要件を持っています。