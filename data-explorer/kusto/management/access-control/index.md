---
title: Kusto Access Control の概要 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の Kusto Access Control の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: ecdcdf22fe25c855045d90e294597c1abc769c03
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862107"
---
# <a name="kusto-access-control-overview"></a>Kusto Access Control の概要

Kusto での Access Control は、次の 2 つのディメンションに基づいています。
* [認証](#authentication):要求を行っているセキュリティ プリンシパルの ID を検証する
* [承認](#authorization): 要求を行っているセキュリティ プリンシパルがターゲット リソースでその要求を行うことを許可されていることを検証する

Kusto クラスター、データベース、またはテーブルに対してクエリまたは制御コマンドを正常に実行するには、アクターが認証と承認の両方を成功させる必要があります。

## <a name="authentication"></a>認証


**Azure Active Directory (AAD)** は、Azure の優先マルチテナント クラウド ディレクトリ サービスであり、セキュリティ プリンシパルの認証や、Microsoft の Active Directory などの他の ID プロバイダーとのフェデレーションに対応できます。

AAD は、Microsoft で Kusto に対して認証を行う場合に推奨される方法です。 次のようなさまざまな認証シナリオがサポートされています。
* **ユーザー認証** (対話型ログオン):人間のプリンシパルを認証するために使用されます。
* **アプリケーション認証** (非対話型ログオン):人間のユーザーがいない状態で実行または認証する必要があるサービスおよびアプリケーションを認証するために使用されます。

### <a name="user-authentication"></a>ユーザー認証
ユーザー認証は、ユーザーが AAD (または、ADFS などの、AAD と連携する ID プロバイダー) に資格情報を提示すると実行され、Kusto サービスに提示できるセキュリティ トークンを受け取ります。 Kusto サービスは、セキュリティ トークンの取得方法は考慮せず、トークンが有効かどうか、および AAD (または連携している IdP) によってどのような情報がそこに入れられているかを考慮します。

クライアント側では、Kusto は、AAD クライアント ライブラリ ADAL または同様のコードがユーザーに資格情報の入力を要求する対話型認証をサポートしています。 また、Kusto を使用するアプリケーションが有効なユーザー トークンを取得して提示する、トークンベースの認証もサポートしています。 最後に、Kusto を使用するアプリケーションが他のサービス (Kusto 以外) の有効なユーザー トークンを取得するシナリオをサポートしています。ただし、そのリソースと Kusto との間に信頼関係があることが条件になります。

[Kusto クライアントライブラリを使用](../../api/connection-strings/kusto.md)して、AAD を kusto に認証する方法の詳細については、kusto 接続文字列に関する説明を参照してください。

### <a name="application-authentication"></a>アプリケーション認証
要求が特定のユーザーに関連付けられていない場合、または資格情報を入力できるユーザーがいない場合は、AAD アプリケーション認証フローを使用できます。 このフローでは、アプリケーションはいくつかの秘密情報を提示することで、AAD (または連携している IdP) に対して認証を行います。 次のシナリオは、さまざまな Kusto クライアントでサポートされています。

* ローカルにインストールされた X.509v2 証明書を使用したアプリケーション認証。
* クライアント ライブラリにバイト ストリームとして提供された X.509v2 証明書を使用したアプリケーション認証。
* AAD アプリケーション ID と AAD アプリケーション キーを使用したアプリケーション認証 (アプリケーションに対するユーザー名/パスワードの認証に相当)。
* 以前に取得した有効な AAD トークン (Kusto に対して発行されたもの) を使用したアプリケーション認証。
* 以前に取得した、他のリソースに対して発行された有効な AAD トークンを使用したアプリケーション認証 (ただし、そのリソースと Kusto との間に信頼関係があることが条件になります)。


### <a name="microsoft-accounts-msas"></a>Microsoft アカウント (MSA)
Microsoft アカウント (MSA) は、Microsoft が管理する、組織以外のすべてのユーザー アカウント (たとえば、`hotmail.com`、`live.com`、`outlook.com`) を示す用語です。
Kusto は、UPN (ユニバーサル プリンシパル名) によって識別される MSA のユーザー認証をサポートしています (セキュリティ グループの概念がないことに注意してください)。
Kusto リソースに MSA プリンシパルが構成されている場合、Kusto は、指定された UPN の解決を試行**しません**。

### <a name="authenticated-sdk-or-rest-calls"></a>認証された SDK 呼び出しまたは REST 呼び出し
* REST API を使用している場合、認証は標準の HTTP `Authorization` ヘッダーを使用して実行されます。
* いずれかの Kusto .NET ライブラリを使用しているとき、認証は、[Kusto 接続文字列](../../api/connection-strings/kusto.md)に認証方法とパラメーターを指定するか、[クライアント要求プロパティ](https://kusto.azurewebsites.net/docs/api/request-properties.html) オブジェクトにプロパティを設定することによって制御されます。

### <a name="kusto-client-sdk-as-an-aad-client-application"></a>AAD クライアント アプリケーションとしての Kusto クライアント SDK
Kusto クライアント ライブラリは、ADAL (AAD クライアント ライブラリ) を呼び出して、Kusto と通信するためのトークンを取得するときに、次の情報を提供します。

1. リソース (クラスター URI。例: `https://Cluster-and-region.kusto.windows.net`)
2. AAD クライアント アプリケーション ID
3. AAD クライアント アプリケーションのリダイレクト URL
4. AAD テナント (これは認証に使用される AAD エンドポイントに影響します。たとえば、AAD テナント `microsoft.com` の場合、AAD エンドポイントは `https://login.microsoftonline.com/microsoft.com` になります)

ADAL によって Kusto クライアント ライブラリに返されるトークンには、オーディエンスとして適切な Kusto クラスター URL と、スコープとして "Access Kusto" アクセス許可が指定されています。

**例: Kusto クラスターの AAD ユーザー トークンを取得する**
```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD TenantID or name}");

// Provide your Application ID and redirect URI
var clientAppID = "{your client app id}";
var redirectUri = new Uri("{your client app redirect uri}");

// acquireTokenTask will receive the bearer token for the authenticated user
var acquireTokenTask = authContext.AcquireTokenAsync(
    $"https://{clusterNameAndRegion}.kusto.windows.net",
    clientAppID,
    redirectUri,
    new PlatformParameters(PromptBehavior.Auto, null)).GetAwaiter().GetResult();
```


## <a name="authorization"></a>承認

認証に使用する方法に関係なく、認証されたすべてのプリンシパルは、Kusto リソースに対してアクションを実行することが許可される前に、承認チェックを受けます。
Kusto は [ロールベースの承認モデル](role-based-authorization.md)を使用します。すなわち、プリンシパルが 1 つ以上の**セキュリティ ロール**に属し、プリンシパルのいずれかのロールが承認されれば、承認は成功します。

たとえば、**データベース ユーザー ロール**は、特定のデータベースのデータの読み取り、データベース内でのテーブルの作成、およびその中での関数の作成を行う権限をセキュリティ プリンシパル (ユーザーまたはサービス) に付与します。

セキュリティ プリンシパルとセキュリティ ロールの関連付けは、個別に、または (AAD に定義されている) セキュリティ グループを使用して定義できます。 これを行うための個々のコマンドは、[ロールベースの承認規則の設定](../security-roles.md)に関する記事に定義されています。
