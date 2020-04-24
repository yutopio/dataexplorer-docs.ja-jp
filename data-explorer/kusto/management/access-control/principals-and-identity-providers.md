---
title: プリンシパルと ID プロバイダー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのプリンシパルと ID プロバイダーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0638ba0031162dadbb4b9a2815940e66d4dcfb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522646"
---
# <a name="principals-and-identity-providers"></a>プリンシパルと ID プロバイダー

Kusto 承認モデルでは、複数の ID プロバイダー (IdP) と複数のプリンシパル型がサポートされています。
この記事では、サポートされているプリンシパルの種類を確認し、[ロール割り当てコマンド](../../management/security-roles.md)での使用について説明します。

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) は、Azure の好ましいマルチテナント クラウド ディレクトリ サービスと ID プロバイダーであり、セキュリティ プリンシパルの認証や、マイクロソフトの Active Directory などの他の ID プロバイダーとのフェデレーションが可能です。

AAD は、Kusto に認証するための推奨される方法です。 次のようなさまざまな認証シナリオがサポートされています。
* **ユーザー認証** (対話型ログオン):人間のプリンシパルを認証するために使用されます。
* **アプリケーション認証** (非対話型ログオン):人間のユーザーがいない状態で実行または認証する必要があるサービスおよびアプリケーションを認証するために使用されます。

>注: Azure Active Directory では、サービス アカウントの認証は許可されません (これは、定義上のオンプレミスの AD エンティティです)。
AD サービス アカウントと同等の AAD は、AAD アプリケーションです。

#### <a name="aad-group-principals"></a>AAD グループ プリンシパル
Kusto はセキュリティ グループ プリンシパルのみをサポートします (配布グループのプリンシパルはサポートしません)。 Kusto クラスタで DG へのアクセスを設定しようとすると、エラーが発生します。

#### <a name="aad-tenants"></a>AAD テナント


>AAD テナントが明示的に指定されていない場合、Kusto は UPN (ユニバーサル プリンシパル名など`johndoe@fabrikam.com`) から解決しようとします (指定されている場合)。
プリンシパルにテナント情報が含まれていない場合 (UPN 形式ではない)、プリンシパル記述子にテナント ID または名前を追加して、明示的に指定する必要があります。

**AAD プリンシパルの例**

|AAD テナント |Type |構文 |
|-----------|-----|-------|
|暗黙的 (UPN)  |User  |`aaduser=`*ユーザー電子メールアドレス*
|明示的な (ID)   |User  |`aaduser=`*ユーザー電子メール アドレス*`;`*テナント ID*または`aaduser=`*オブジェクト ID*`;`*テナント ID*
|明示的 (名前) |User  |`aaduser=`*ユーザー電子メール アドレス*`;`*テナント名*または`aaduser=`*オブジェクト ID*`;`*テナント名*
|暗黙的 (UPN)  |グループ |`aadgroup=`*グループ電子メールアドレス*
|明示的な (ID)   |グループ |`aadgroup=`*グループオブジェクト Id*`;`*テナント ID*または`aadgroup=`*グループ表示名*`;`*テナント ID*
|明示的 (名前) |グループ |`aadgroup=`*グループオブジェクト ID*`;`*テナント名*または`aadgroup=`*グループ表示名*`;`*テナント名*
|明示的 (UPN)  |アプリ   |`aadapp`=*アプリケーション表示名*`;`*テナント ID*
|明示的 (名前) |アプリ   |`aadapp=`*アプリケーション ID*`;`*テナント名*

```kusto
// No need to specify AAD tenant for UPN, as Kusto performs the resolution by itself
.add database Test users ('aaduser=imikeoein@fabrikam.com') 'Test user (AAD)'

// AAD SG on 'fabrikam.com' tenant
.add database Test users ('aadgroup=SGDisplayName;fabrikam.com') 'Test group @fabrikam.com (AAD)'

// AAD App on 'fabrikam.com' tenant - by tenant name
.add database Test users ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com') 'Test app @fabrikam.com (AAD)'
```

### <a name="microsoft-accounts-msas"></a>Microsoft アカウント (MSA)
Microsoft アカウント (MSA) は、Microsoft が管理する、組織以外のすべてのユーザー アカウント (たとえば、`hotmail.com`、`live.com`、`outlook.com`) を示す用語です。
Kusto は、UPN (ユニバーサル プリンシパル名) によって識別される MSA のユーザー認証をサポートしています (セキュリティ グループの概念がないことに注意してください)。
Kusto リソースに MSA プリンシパルが構成されている場合、Kusto は、指定された UPN の解決を試行**しません**。

**MSA プリンシパルの例**

|IdP  |Type  |構文 |
|-----|------|-------|
|Live.com |User  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

