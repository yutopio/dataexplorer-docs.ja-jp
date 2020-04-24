---
title: 方法 - AAD アプリケーションを作成する - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの AAD アプリケーションの作成方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 91d646d3bd0922f9daf9e8caec6fa6be5e32e2b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522901"
---
# <a name="howto-creating-an-aad-application"></a>方法: AAD アプリケーションを作成する

AAD ユーザー認証は非常に簡単ですが (ユーザーはテナント管理者 (MSIT など) によって AAD で定義されるため、AAD アプリケーション認証はやや複雑です。 これは、アプリケーションを作成して AAD に登録する必要があるためです。 このプロセスは、以下に詳細に説明します。

AAD アプリケーション認証は、ユーザーがログオンまたは存在しない場合に Kusto にアクセスする必要があるアプリケーション (無人サービスやスケジュールされたフローなど) に役立ちます。

対話的なユーザー コンテキストを持つクライアント アプリケーションと中間層アプリケーションでは、このモデルを回避する必要があります。 これは、承認はユーザー ID ではなく AAD アプリケーション ID に基づいて実行されるため、呼び出し側アプリケーションは、誤用を防ぐために独自の承認ロジックを実装する必要があるためです。

## <a name="application-authentication-use-cases"></a>アプリケーション認証のユースケース

アプリケーション認証を利用する主なシナリオは 2 つあります。
* Kusto サービスに直接、および自分の代わりに連絡するアプリケーション
* Kusto に対してユーザーを認証するアプリケーション (委任認証)

## <a name="1-provisioning-a-new-application"></a>1. 新しいアプリケーションのプロビジョニング

#### <a name="register-the-new-application"></a>新しいアプリケーションを登録する

1. [Azure ポータル](https://portal.azure.com)にログインし、ブレード`Azure Active Directory`を開きます。

    ![alt text](./images/Aad-create-app-step-0.png "Aad-create-app-step-0")

1. ブレード`App registrations`がロードされたら、 を選択`New application registration`します。

    ![alt text](./images/Aad-create-app-step-1.png "Aad-create-app-step 1")

1. アプリケーションの詳細を入力します。
    * 名前
    * タイプ: に設定`Web app/API`
    * サインオン URL: ユーザーがアプリケーションにアクセスするために使用する URL。 AAD は URL を検証しません。<br>
        しかし、あなたが値を提供することを義務付けています。 したがって、アプリケーションにユーザーがアクセスできる URL がない場合は、<br>
        アプリケーションに属する URL (https://<APP-CNAME>または https://<クラウドサービス名> cloudapp.net など) を入力します。<br>
        値を指定して、アプリケーションがまだ作成されていない場合は続行することもできます。

    ![alt text](./images/Aad-create-app-step-2.png "Aad-create-app-ステップ2")

1. アプリケーションの準備ができたら、そのブレードを`Settings`開きます。

    ![alt text](./images/Aad-create-app-step-3.png "Aad-create-app-step-3")

1. ブレードで`Keys`、新しいアプリケーションの認証を設定します。
    * 共有キーを使用する場合は、ドロップダウン メニューからキーの期間を選択し、生成されたときにキーをコピーします。
        このキーを復元することはできません。
    * または、X509 証明書を使用してアプリケーションを認証します。
        これを行うには、証明書`Upload Public Key`の公開部分をアップロードする指示に従って選択します。
    * 終わったら、忘れずに`Save`選択してください。

    ![alt text](./images/Aad-create-app-step-4.png "Aad-create-app-step-4")

アプリケーションがセットアップされています。 必要な場合は、新しく作成されたアプリケーションを使用して Kusto にアクセスするだけで済みます。

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Kusto サービス アプリケーションの委任されたアクセス許可を設定します

Kusto アクセスのユーザー認証またはアプリ認証をアプリケーションで実行できるようにする必要がある場合は、アプリケーションと Kusto の間に信頼を設定する必要があります。

1. ブレードで`Required permissions`、 を`Add`選択します。

    ![alt text](./images/Aad-create-app-step-5.png "Aad-create-app-step-5")

1. を`Select an API`選択し`KustoService`、フィルタボックスに入力して`KustoService (Kusto)`を選択します。
1. Kusto クラスタで MFA が`KustoMFA`必要な場合は、フィルタ`KustoServiceMFA (KustoMFA)`ボックスに入力して を選択します。

    ![alt text](./images/Aad-create-app-step-6.png "Aad-create-app-step 6")

1. 選択内容を確認したら、 の委任されたアクセス`Access Kusto`許可を選択します。

    ![alt text](./images/Aad-create-app-step-7.png "Aad-create-app-step 7")

1. プロセス`Done`を完了する場合に選択します。



### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Kusto クラスタ上のアプリケーションに対するアクセス許可を設定する

> 新しくプロビジョニングされたアプリケーションを使用する前に、Graph API から解決されていることを確認します。<br>
    AAD の変更が反映されるまでに数時間かかることがあります。

1. データベース管理者にアクセスして、新しいアプリに権限を付与します。
権限の設定の詳細については[、「データベース プリンシパルの管理](../security-roles.md)」セクションを参照してください。<br>
インジェクション権限の設定については、「イン[ジェクション権限](../../api/netfx/kusto-ingest-client-permissions.md)」を参照してください。
1. まだない場合は、Kusto エクスプローラでこのサービスへの接続を追加します。

### <a name="3-application-can-now-access-kusto"></a>3. アプリケーションが Kusto にアクセスできるようになりました

新しくプロビジョニングされたアプリケーションを使用して Kusto クライアント ライブラリを使用して Kusto クラスタにアクセスする場合は、次の接続文字列を指定します (共有キーで認証する場合)。

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*アプリケーションキーを指定*`;Application Key=`*します*。


### <a name="appendix-a-aad-errors"></a>付録 A: AAD エラー

#### <a name="aad-error-aadsts650057"></a>AAD エラー AADSTS650057

アプリケーションを使用して Kusto アクセスのユーザーまたはアプリケーションを認証する場合、Kusto サービス アプリケーションの委任されたアクセス許可を設定する必要があります。
そうしないと、認証が試行されたときに、次のようなエラーが発生します。

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

[Kusto サービス アプリケーションの委任されたアクセス許可を設定する手順に](#set-up-delegated-permissions-for-kusto-service-application)従う必要があります。

#### <a name="enable-user-consent-if-needed"></a>必要に応じてユーザーの同意を有効にする

AAD テナント管理者は、テナント ユーザーがアプリケーションに同意できないようにするポリシーを制定する場合があります。 この場合、ユーザーがアプリケーションにログインしようとすると、次のようなエラーが発生します。

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

テナント内のすべてのユーザーに同意を与える、または特定のアプリケーションのユーザーの同意を有効にする AAD 管理者に依頼する必要があります。



### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>付録 B: 高度なトピックとトラブルシューティング

* サポートされている接続文字列の完全なリファレンスについては[、Kusto](../../api/connection-strings/kusto.md)接続文字列のドキュメントを参照してください。
