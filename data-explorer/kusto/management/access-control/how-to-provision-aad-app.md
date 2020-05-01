---
title: HowTo-AAD アプリケーションの作成-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーで AAD アプリケーションを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 0816d55cda6d29820084514b0bfec27d726693f9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617971"
---
# <a name="howto-creating-an-aad-application"></a>HowTo: AAD アプリケーションの作成

AAD ユーザー認証は非常に簡単ですが (ユーザーは、MSIT などのテナント管理者によって AAD で定義されているため)、AAD アプリケーション認証はやや複雑になります。 これは、AAD でアプリケーションを作成して登録する必要があるためです。 このプロセスの詳細については、以下で説明します。

AAD アプリケーション認証は、ユーザーがログオンしていない、または存在しない場合 (自動サービスやスケジュールされたフローなど)、Kusto にアクセスする必要があるアプリケーションに役立ちます。

対話型ユーザーコンテキストを持つクライアントアプリケーションと中間層アプリケーションは、このモデルを回避する必要があります。 これは、承認はユーザー id ではなく AAD アプリケーションの id に基づいて実行されるため、呼び出し元のアプリケーションは、誤用を防ぐために独自の承認ロジックを実装する必要があるためです。

## <a name="application-authentication-use-cases"></a>アプリケーション認証のユースケース

アプリケーション認証を使用する主なシナリオには、次の2つがあります。
* Kusto サービスと直接通信するアプリケーション
* Kusto に対してユーザーを認証するアプリケーション (委任された認証)

## <a name="1-provisioning-a-new-application"></a>1. 新しいアプリケーションをプロビジョニングする

#### <a name="register-the-new-application"></a>新しいアプリケーションを登録する

1. [Azure portal](https://portal.azure.com)にログインし、 `Azure Active Directory`ブレードを開きます。

    :::image type="content" source="images/aad-create-app-step-0.png" alt-text="Aad のアプリ作成手順0":::

1. `App registrations`ブレードが読み込ま`New application registration`れたら、を選択します。

    :::image type="content" source="images/aad-create-app-step-1.png" alt-text="Aad のアプリ作成手順1":::

1. アプリケーションの詳細を入力します。
    * 名前
    * 種類: に設定します。`Web app/API`
    * サインオン URL: アプリケーションにアクセスするためにユーザーが使用する URL。 AAD は URL を検証しません。<br>
        ただし、値を指定する必要があります。 アプリケーションにユーザーがアクセスできる URL がない場合、<br>
        アプリケーションに属している URL を入力します (https://<> または https://<CLOUD-SERVICE-NAME>. cloudapp.net)。<br>
        アプリケーションがまだ書き込まれていない場合は、値を指定して続行することもできます。

    
    :::image type="content" source="images/aad-create-app-step-2.png" alt-text="Aad のアプリ作成手順2":::

1. アプリケーションの準備ができたら、 `Settings`ブレードを開きます。

    :::image type="content" source="images/aad-create-app-step-3.png" alt-text="Aad 作成手順3":::

1. `Keys`ブレードで、新しいアプリケーションの認証を設定します。
    * 共有キーを使用する場合は、ドロップダウンメニューから [キー期間] を選択し、生成されたキーをコピーします。
        このキーを復元することはできません。
    * または、X509 証明書を使用してアプリケーションを認証します。
        これを行うには`Upload Public Key` 、を選択し、指示に従って証明書の公開部分をアップロードします。
    * 完了したら、 `Save`忘れずに選択してください。

    :::image type="content" source="images/aad-create-app-step-4.png" alt-text="Aad のアプリ作成手順4":::

アプリケーションがセットアップされています。 新しく作成されたアプリケーションで Kusto にアクセスするだけでよい場合は、完了です。

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Kusto サービスアプリケーションの委任されたアクセス許可を設定する

Kusto にアクセスするユーザーまたはアプリの認証をアプリケーションで実行できるようにするには、アプリケーションと Kusto の間に信頼関係を設定する必要があります。

1. `Required permissions`ブレードで、を選択`Add`します。

    :::image type="content" source="images/aad-create-app-step-5.png" alt-text="Aad のアプリ作成手順5":::
   
1. を`Select an API`選択し`KustoService` 、フィルターボックスに「」 `KustoService (Kusto)`と入力して、を選択します。
1. Kusto クラスターで MFA が必要な場合`KustoMFA`は、フィルターボックスに「 `KustoServiceMFA (KustoMFA)`」と入力し、を選択します。

    :::image type="content" source="images/aad-create-app-step-6.png" alt-text="Aad のアプリ作成手順6":::

1. 選択内容を確認した後、の`Access Kusto`[委任されたアクセス許可] を選択します。

   :::image type="content" source="images/aad-create-app-step-7.png" alt-text="Aad のアプリ作成手順7":::

1. プロセス`Done`を完了する場合に選択します。


### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Kusto クラスター上のアプリケーションにアクセス許可を設定する

> 新しくプロビジョニングされたアプリケーションを使用する前に、Graph API から解決されることを確認します。<br>
    AAD の変更が反映されるまでに、最大で数時間かかることがあります。

1. 新しいアプリにアクセス許可を付与するには、データベース管理者にアクセスしてください。
アクセス許可の設定の詳細については、「[データベースプリンシパルの管理](../security-roles.md)」セクションを参照してください。<br>
インジェストアクセス許可の設定については、[インジェストのアクセス許可](../../api/netfx/kusto-ingest-client-permissions.md)に関する情報を参照してください。
1. まだ使用していない場合は、Kusto エクスプローラーにこのサービスへの接続を追加します。

### <a name="3-application-can-now-access-kusto"></a>3. アプリケーションが Kusto にアクセスできるようになりました。

新しくプロビジョニングされたアプリケーションを使用して Kusto クライアントライブラリを使用して Kusto クラスターにアクセスする場合は、次の接続文字列を指定します (共有キーで認証することを選択した場合)。

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*applicationclientid*`;Application Key=`*applicationclientid*


### <a name="appendix-a-aad-errors"></a>付録 A: AAD エラー

#### <a name="aad-error-aadsts650057"></a>AAD エラー AADSTS650057

アプリケーションを使用して Kusto にアクセスするユーザーまたはアプリケーションを認証する場合は、Kusto サービスアプリケーションの委任されたアクセス許可を設定する必要があります。つまり、Kusto にアクセスするユーザーまたはアプリケーションをアプリケーションで認証できることを宣言します。
そうしないと、認証が試行されたときに、次のようなエラーが発生します。

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

「 [Kusto サービスアプリケーションの委任](#set-up-delegated-permissions-for-kusto-service-application)されたアクセス許可を設定する」の手順に従う必要があります。

#### <a name="enable-user-consent-if-needed"></a>必要に応じてユーザーの同意を有効にする

AAD テナント管理者は、テナントユーザーがアプリケーションに同意しないようにするポリシーを施行することがあります。 これにより、ユーザーがアプリケーションにログインしようとすると、次のようなエラーが発生します。

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

AAD 管理者に依頼して、テナント内のすべてのユーザーに同意を付与するか、特定のアプリケーションに対するユーザーの同意を有効にする必要があります。

### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>付録 B: 高度なトピックとトラブルシューティング

* サポートされている接続文字列の完全なリファレンスについては、 [Kusto 接続文字列](../../api/connection-strings/kusto.md)に関するドキュメントを参照してください。
