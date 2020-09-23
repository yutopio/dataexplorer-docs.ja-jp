---
title: Kusto アクセス制御の概要 - Azure Data Explorer
description: この記事では、Azure Data Explorer の Kusto Access Control の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: 7b97d62e007b5294bf776fb5d5adcbac435056ef
ms.sourcegitcommit: 3fc8e9b6a313a863916031d4beba84123edcf123
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847855"
---
# <a name="kusto-access-control-overview"></a>Kusto Access Control の概要

Azure Data Explorer のアクセス制御は、主に次の 2 つの要因によって決まります。
* [認証](#authentication):要求を行っているセキュリティ プリンシパルの ID を検証する
* [承認](#authorization): 要求を行っているセキュリティ プリンシパルがターゲット リソースでその要求を行うことを許可されていることを検証する

Azure Data Explorer クラスター、データベース、テーブルのクエリまたは制御コマンドは、認証と承認の両方のチェックに合格する必要があります。

## <a name="authentication"></a>認証

**Azure Active Directory (Azure AD)** は、Azure の優先マルチテナント クラウド ディレクトリ サービスです。 セキュリティ プリンシパルの認証や、その他の ID プロバイダーとのフェデレーションを行うことができます。

Azure AD は、Microsoft で Azure Data Explorer に対する認証方法として推奨されています。 次のようなさまざまな認証シナリオがサポートされています。
* **ユーザー認証** (対話型サインイン):人間のプリンシパルを認証するために使用されます。
* **アプリケーション認証** (非対話型サインイン):人間のユーザーがいない状態で実行および認証する必要があるサービスおよびアプリケーションを認証するために使用されます。

### <a name="user-authentication"></a>ユーザー認証

ユーザー認証は、以下に対してユーザーが資格情報を提示するときに実行されます。
* Azure AD 
* Azure AD で使用できる ID プロバイダー

成功した場合は、ユーザーは Azure Data Explorer サービスに提示できるセキュリティ トークンを受け取ります。 Azure Data Explorer サービスでは、セキュリティ トークンの取得方法は考慮されません。 トークンが有効であるかどうか、Azure AD (またはフェデレーションされている IdP) によってどのような情報が提供されるかが考慮されます。

クライアント側では、Microsoft Authentication Library または同様のコードによって、ユーザーに資格情報の入力が要求される、対話型認証が Azure Data Explorer でサポートされています。 また、Azure Data Explorer を使用するアプリケーションが有効なユーザー トークンを取得する、トークンベースの認証もサポートされています。 Azure Data Explorer を使用するアプリケーションでは、別のサービスに対して有効なユーザー トークンを取得することもできます。 このユーザー トークンは、そのリソースと Azure Data Explorer の間に信頼関係がある場合にのみ取得できます。

Kusto クライアント ライブラリを使用し、Azure AD を使用して Azure Data Explorer に対する認証を行う方法の詳細については、「[Kusto の接続文字列](../../api/connection-strings/kusto.md)」を参照してください。

### <a name="application-authentication"></a>アプリケーション認証

要求が特定のユーザーに関連付けられていない場合、または資格情報を入力できるユーザーがいない場合は、Azure AD アプリケーション認証フローを使用します。 このフローでは、アプリケーションは何らかのシークレット情報を提示することで、Azure AD (またはフェデレーションされている IdP) に対して認証を行います。 次のシナリオは、さまざまな Azure Data Explorer クライアントでサポートされています。

* ローカルにインストールされた X.509v2 証明書を使用したアプリケーション認証
* クライアント ライブラリにバイト ストリームとして提供された X.509v2 証明書を使用したアプリケーション認証
* Azure AD アプリケーション ID と Azure AD アプリケーション キーを使用したアプリケーション認証。

    > [!NOTE] 
    > ID とキーは、ユーザー名とパスワードに相当します

* 以前に取得した有効な Azure AD トークン (Azure Data Explorer に対して発行されたもの) を使用したアプリケーション認証。
* 以前に取得した有効な Azure AD トークン (他のリソースに対して発行されたもの) を使用したアプリケーション認証。 このメソッドは、そのリソースと Azure Data Explorer の間に信頼関係がある場合に機能します。

### <a name="microsoft-accounts-msas"></a>Microsoft アカウント (MSA)

Microsoft アカウント (MSA) は、Microsoft が管理する、組織以外のすべてのユーザー アカウント (たとえば、`hotmail.com`、`live.com`、`outlook.com`) を示す用語です。
Kusto では、ユーザー プリンシパル名 (UPN) によって識別される MSA のユーザー認証がサポートされています (セキュリティ グループの概念はありません)。

Azure Data Explorer リソースで MSA プリンシパルが構成されている場合、Azure Data Explorer は、提供された UPN を解決**しません**。

### <a name="authenticated-sdk-or-rest-calls"></a>認証された SDK 呼び出しまたは REST 呼び出し

* REST API を使用している場合、認証は標準の HTTP `Authorization` ヘッダーを使用して実行されます
* いずれかの Azure Data Explorer .NET ライブラリを使用している場合、認証は、[接続文字列](../../api/connection-strings/kusto.md)に認証方法とパラメーターを指定することによって制御されます。 [クライアント要求プロパティ](../../api/netfx/request-properties.md) オブジェクトにプロパティを設定する方法もあります。

### <a name="azure-data-explorer-client-sdk-as-an-azure-ad-client-application"></a>Azure AD クライアント アプリケーションとしての Azure Data Explorer クライアント SDK

Kusto クライアント ライブラリは、Microsoft Authentication Library を呼び出して、Kusto と通信するためのトークンを取得するときに、次の情報を提供します。

* リソース (クラスター URI。例: `https://Cluster-and-region.kusto.windows.net`)
* Azure AD クライアント アプリケーション ID
* Azure AD クライアント アプリケーションのリダイレクト URL
* 認証に使用される Azure AD エンドポイントに影響する Azure AD テナント。 たとえば、Azure AD テナント `microsoft.com` の場合、Azure AD エンドポイントは `https://login.microsoftonline.com/microsoft.com`) です

Microsoft Authentication Library から Azure Data Explorer クライアント ライブラリに返されるトークンには、対象ユーザーとして適切な Azure Data Explorer クラスター URL、スコープとして "Azure Data Explorer へのアクセス" のアクセス許可があります。

**例:Azure Data Explorer クラスターの Azure AD ユーザー トークンを取得する**

```csharp
// Create Auth Context for Azure AD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD TenantID or name}");

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

認証されたすべてのプリンシパルでは、Azure Data Explorer リソースに対してアクションを実行する前に、承認チェックが行われます。
Azure Data Explorer は[ロールベースの承認モデル](role-based-authorization.md)を使用します。このモデルでは、プリンシパルが 1 つまたは複数のセキュリティ ロールに割り当てられます。 プリンシパルのいずれかのロールが承認されていれば、承認は成功します。

たとえば、データベース ユーザー ロールは、セキュリティ プリンシパル、ユーザー、サービスに次の権限を付与します。
* 特定のデータベースのデータを読み取る
* データベースのテーブルを作成する
* データベースの関数を作成する

セキュリティ プリンシパルとセキュリティ ロールの関連付けは、個別に、または Azure AD に定義されているセキュリティ グループを使用して定義できます。 コマンドは、[ロールベースの承認規則の設定](../security-roles.md)に関するページで定義されています。
