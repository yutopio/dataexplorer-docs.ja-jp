---
title: アクセスのために AAD を使用した kusto-Azure データエクスプローラー
description: この記事では、azure データエクスプローラーで Azure データエクスプローラーアクセスを行うために AAD で認証 How-To について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref, devx-track-js
ms.date: 09/13/2019
ms.openlocfilehash: 65e15fca7c7a69e1c9ba2d79f7dca531304ea91b
ms.sourcegitcommit: 8a7165b28ac6b40722186300c26002fb132e6e4a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92749473"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Azure データエクスプローラーアクセス用に AAD で認証 How-To

Azure データエクスプローラーにアクセスするには、 **Azure Active Directory** サービスに対して認証を行うことをお勧めします ( **Azure AD** または単に **AAD** とも呼ばれることもあります)。 これにより、Azure データエクスプローラーでは、2段階のプロセスを使用して、アクセスプリンシパルのディレクトリ資格情報を確認することができなくなります。

1. 最初の手順では、クライアントは AAD サービスと通信して認証を行い、クライアントがアクセスする特定の Azure データエクスプローラーエンドポイントに対して特別に発行されたアクセストークンを要求します。
2. 2番目のステップでは、クライアントは Azure データエクスプローラーに要求を発行し、最初の手順で取得したアクセストークンを Azure データエクスプローラーに対する id の証明として提供します。

その後、Azure データエクスプローラーは AAD がアクセストークンを発行したセキュリティプリンシパルに代わって要求を実行し、すべての承認チェックはこの id を使用して実行されます。

ほとんどの場合は、Azure データエクスプローラー Sdk のいずれかを使用してサービスにプログラムでアクセスすることをお勧めします。これにより、上記のフロー (およびその他の多く) を実装する手間が大幅に排除されます。 例については、「 [.NET SDK](../../api/netfx/about-the-sdk.md)」を参照してください。
その後、認証プロパティは [Kusto 接続文字列](../../api/connection-strings/kusto.md)によって設定されます。
これが不可能な場合は、このフローを自分で実装する方法の詳細について、「」を参照してください。

主な認証シナリオは次のとおりです。

* **サインインしているユーザーを認証するクライアントアプリケーション** 。
  このシナリオでは、対話型 (クライアント) アプリケーションは、資格情報 (ユーザー名とパスワードなど) に対して AAD プロンプトをユーザーに対してトリガーします。
  「 [ユーザー認証](#user-authentication)」を参照してください。

* **"ヘッドレス" アプリケーション** 。
  このシナリオでは、資格情報を提供するユーザーが存在せずにアプリケーションが実行されており、アプリケーションは、構成されている資格情報を使用して、AAD に対して "自身" として認証されます。
  「 [アプリケーション認証](#application-authentication)」を参照してください。

* **代理認証** 。
  このシナリオでは、"web サービス" または "web アプリ" シナリオと呼ばれることもあり、アプリケーションは別のアプリケーションから AAD アクセストークンを取得し、それを Azure データエクスプローラーで使用できる別の AAD アクセストークンに "変換" します。
  つまり、アプリケーションは、資格情報と Azure データエクスプローラーサービスを提供したユーザーまたはアプリケーションの仲介として機能します。
  [認証については、](#on-behalf-of-authentication)「」を参照してください。

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Azure データエクスプローラーの AAD リソースの指定

AAD からアクセストークンを取得する場合、クライアントは、トークンを発行する必要がある aad **リソース** を aad に通知する必要があります。 Azure データエクスプローラーエンドポイントの AAD リソースは、ポート情報とパスを除いて、エンドポイントの URI です。 次に例を示します。

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>AAD テナント ID の指定

AAD はマルチテナントサービスであり、すべての組織は AAD で **ディレクトリ** と呼ばれるオブジェクトを作成できます。 ディレクトリオブジェクトは、ユーザーアカウント、アプリケーション、グループなどのセキュリティ関連のオブジェクトを保持します。 AAD は、多くの場合、ディレクトリを **テナント** として参照します。 AAD テナントは GUID ( **テナント ID** ) によって識別されます。 多くの場合、AAD テナントは、組織のドメイン名で識別することもできます。

たとえば、"Contoso" という組織には、テナント ID とドメイン名が含まれている場合があり `4da81d62-e0a8-4899-adad-4349ca6bfe24` `contoso.com` ます。

## <a name="specifying-the-aad-authority"></a>AAD 機関の指定

AAD には、認証用のエンドポイントがいくつかあります。

* 認証されているプリンシパルをホストしているテナントがわかっている場合 (つまり、ユーザーまたはアプリケーションが属する AAD ディレクトリを認識している場合)、AAD エンドポイントはに `https://login.microsoftonline.com/{tenantId}` なります。
  ここで `{tenantId}` は、AAD の組織のテナント ID またはそのドメイン名 (など) のいずれかになり `contoso.com` ます。

* 認証されているプリンシパルをホストしているテナントが不明な場合は、上記のを値に置き換えることによって "common" エンドポイントを使用でき `{tenantId}` `common` ます。

> [!NOTE]
> 認証に使用される AAD エンドポイントは、 **aad 機関 URL** または単に **aad 機関** とも呼ばれます。

## <a name="aad-token-cache"></a>AAD トークンキャッシュ

Azure データエクスプローラー SDK を使用する場合、AAD トークンは、ユーザーごとのトークンキャッシュ (サインインしたユーザーによってのみアクセスまたは復号化できる **%APPDATA%\Kusto\tokenCache.data** という名前のファイル) にローカルコンピューターに格納されます。キャッシュは、ユーザーに資格情報の入力を求める前にトークンを検査します。これにより、ユーザーが資格情報を入力する回数が大幅に短縮されます。

> [!NOTE]
> AAD トークンキャッシュを使用すると、ユーザーが Azure データエクスプローラーにアクセスする際に表示される対話型プロンプトの数を減らすことができますが、完了することはできません。 さらに、ユーザーが資格情報の入力を求められるときに事前に予測することはできません。
> つまり、対話型でないログオンをサポートする必要がある場合 (たとえば、タスクをスケジュールする場合など)、ユーザーアカウントを使用して Azure データエクスプローラーにアクセスしないようにする必要があります。これは、ログオンしたユーザーに資格情報の入力を求めるときに、非対話型ログオンで実行されている場合、プロンプトが失敗するためです。


## <a name="user-authentication"></a>ユーザー認証

ユーザー認証を使用して Azure データエクスプローラーにアクセスする最も簡単な方法は、Azure データエクスプローラー SDK を使用して、 `Federated Authentication` azure データエクスプローラー接続文字列のプロパティをに設定することです `true` 。 SDK を初めて使用してサービスに要求を送信すると、AAD 資格情報を入力するためのサインインフォームがユーザーに表示され、認証が成功すると要求が送信されます。

Azure データエクスプローラー SDK を使用しないアプリケーションでも、AAD サービスセキュリティプロトコルクライアントを実装する代わりに、AAD クライアントライブラリ (ADAL) を使用できます。 https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet.Net アプリケーションから実行する例については、[] を参照してください。

Azure データエクスプローラーアクセスのユーザーを認証するには、まず、アプリケーションに委任されたアクセス許可が付与されている必要があり `Access Kusto` ます。 詳細については、 [AAD アプリケーションのプロビジョニングに関する Kusto のガイドを](../../../provision-azure-ad-app.md#configure-delegated-permissions-for-the-application-registration) 参照してください。

次の簡単なコードスニペットは、ADAL を使用して Azure データエクスプローラーにアクセスするための AAD ユーザートークンを取得する方法を示しています (ログオン UI を起動します)。

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

次の簡単なコードスニペットは、ADAL を使用して Azure データエクスプローラーにアクセスする AAD アプリケーショントークンを取得する方法を示しています。 このフローでは、プロンプトは表示されません。アプリケーションは AAD に登録され、アプリケーション認証を実行するために必要な資格情報 (AAD によって発行されたアプリキーや AAD に事前登録されている X509v2 証明書など) を備えたアプリケーションである必要があります。

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

このシナリオでは、アプリケーションは、アプリケーションによって管理されている任意のリソースの AAD アクセストークンを送信し、そのトークンを使用して Azure データエクスプローラーリソースの新しい AAD アクセストークンを取得します。これにより、元の AAD アクセストークンによって示されるプリンシパルに代わってアプリケーションが Kusto にアクセスできるようになります。

このフローは、 [OAuth2 トークン交換フロー](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)と呼ばれます。
通常、AAD では複数の構成手順が必要であり、場合によっては (AAD テナント構成によっては) AAD テナントの管理者からの特別な同意が必要になる場合があります。



**手順 1: アプリケーションと Azure データエクスプローラーサービスの間に信頼関係を確立する**

1. [Azure portal](https://portal.azure.com/)を開き、正しいテナントにサインインしていることを確認します (ポータルへのサインインに使用する id については、上部/右コーナーを参照してください)。

2. [リソース] ウィンドウで、[ **Azure Active Directory** ]、[ **アプリの登録** ] の順にクリックします。

3. 代理フローを使用するアプリケーションを見つけて開きます。

4. [ **API のアクセス許可** ] をクリックし、 **アクセス許可を追加** します。

5. **Azure データエクスプローラー** という名前のアプリケーションを検索し、それを選択します。

6. [ **User_impersonation/アクセス Kusto** ] を選択します。

7. [ **アクセス許可の追加** ] をクリックします。

**手順 2: サーバーコードでトークン交換を実行する**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for a Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**手順 3: Kusto クライアントライブラリにトークンを提供し、クエリを実行する**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}",
    clusterName,
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Web クライアント (JavaScript) の認証と承認



**AAD アプリケーションの構成**

> [!NOTE]
> AAD アプリをセットアップするために従う必要がある標準の [手順](../../../provision-azure-ad-app.md) に加えて、aad アプリケーションで oauth 暗黙的フローを有効にする必要もあります。 これを実現するには、azure portal のアプリケーションページで [マニフェスト] を選択し、[oauth2AllowImplicitFlow] を [true] に設定します。

**詳細**

クライアントがユーザーのブラウザーで実行されている JavaScript コードである場合は、暗黙的な許可フローが使用されます。 クライアントアプリケーションに Azure データエクスプローラーサービスへのアクセスを許可するトークンは、リダイレクト URI (URI フラグメント) の一部として正常に認証された直後に提供されます。このフローでは更新トークンが指定されていないため、クライアントは長時間にわたってトークンをキャッシュして再利用することはできません。

Native client フローの場合と同様に、2つの AAD アプリケーション (サーバーとクライアント) に関係が構成されている必要があります。

AdalJs を実行するには、access_token の呼び出しが行われる前に id_token を取得する必要があります。

アクセストークンはメソッドを呼び出すことによって取得され、 `AuthenticationContext.login()` access_tokens を呼び出すことによって取得され `Authenticationcontext.acquireToken()` ます。

* 適切な構成で AuthenticationContext を作成します。

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* `authContext.login()` `acquireToken()` ログインしていない場合は、を試行する前にを呼び出します。 ログインされているかどうかを知るには、を呼び出して、それが返されるかどうかを確認することをお勧めします `authContext.getCachedUser()` `false` 。
* `authContext.handleWindowCallback()`ページが読み込まれるたびに、を呼び出します。 これは、AAD からリダイレクトをインターセプトし、そのトークンをフラグメント URL から取得してキャッシュするコードの部分です。
* を呼び出して、 `authContext.acquireToken()` 実際のアクセストークンを取得します。これで、有効な ID トークンが作成されました。 AcquireToken の最初のパラメーターは、Kusto サーバー AAD アプリケーションリソース URL になります。

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* Azure データエクスプローラー要求では、トークンをベアラートークンとして使用できます。 次に例を示します。

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

> 警告-認証時に次の例外または同様の例外が発生した場合: `ReferenceError: AuthenticationContext is not defined`
これは、グローバル名前空間に AuthenticationContext がないことが原因である可能性があります。
残念ながら、AdalJS には、認証コンテキストがグローバル名前空間で定義されるという、ドキュメントに記載されていない要件があります。