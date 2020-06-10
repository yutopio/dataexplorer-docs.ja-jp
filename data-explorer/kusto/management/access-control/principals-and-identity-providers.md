---
title: プリンシパルと Id プロバイダー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのプリンシパルと Id プロバイダーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e34c724799cffe38db93869e96fcfae83a92b55
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664995"
---
# <a name="principals-and-identity-providers"></a>プリンシパルと Id プロバイダー

Kusto 認証モデルでは、複数の Id プロバイダー (IdPs) と複数のプリンシパルの種類がサポートされています。
この記事では、サポートされているプリンシパルの種類を確認し、[ロールの割り当てコマンド](../../management/security-roles.md)での使用方法を示します。

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) は、Azure の優先マルチテナントクラウドディレクトリサービスと Id プロバイダーであり、セキュリティプリンシパルの認証や、Microsoft の Active Directory などの他の id プロバイダーとのフェデレーションを可能にします。

AAD は、Kusto に対する認証方法として推奨されています。 次のようなさまざまな認証シナリオがサポートされています。
* **ユーザー認証** (対話型ログオン):人間のプリンシパルを認証するために使用されます。
* **アプリケーション認証** (非対話型ログオン):人間のユーザーがいない状態で実行または認証する必要があるサービスおよびアプリケーションを認証するために使用されます。

> [!NOTE]
> Azure Active Directory では、(オンプレミスの AD エンティティを定義することによって) サービスアカウントの認証は許可されません。
AAD と同等の AD サービスアカウントは AAD アプリケーションです。

#### <a name="aad-group-principals"></a>AAD グループプリンシパル
Kusto でサポートされるのはセキュリティグループプリンシパルだけです (配布グループのプリンシパルではありません)。 Kusto クラスターの DG へのアクセスを設定しようとすると、エラーが発生します。

#### <a name="aad-tenants"></a>AAD テナント

AAD テナントが明示的に指定されていない場合、Kusto は UPN (UniversalPrincipalName、など) からの解決を試み `johndoe@fabrikam.com` ます (指定されている場合)。 (UPN 形式ではなく) テナント情報がプリンシパルに含まれていない場合は、テナント ID または名前をプリンシパル記述子に追加することによって、明示的に指定する必要があります。

**AAD プリンシパルの例**

|AAD テナント |Type |構文 |
|-----------|-----|-------|
|暗黙的 (UPN)  |User  |`aaduser=`*UserEmailAddress*
|明示的 (ID)   |User  |`aaduser=`*UserEmailAddress* `;`*Tenantid*または `aaduser=` *ObjectID* `;` *tenantid*
|明示的 (名前) |User  |`aaduser=`*UserEmailAddress* `;`*Tenantname*または `aaduser=` *ObjectID* `;` *tenantname*
|暗黙的 (UPN)  |グループ |`aadgroup=`*GroupEmailAddress*
|明示的 (ID)   |グループ |`aadgroup=`*GroupObjectId* `;`*Tenantid*または `aadgroup=` *groupdisplayname* `;` *tenantid*
|明示的 (名前) |グループ |`aadgroup=`*GroupObjectId* `;`*Tenantname*または `aadgroup=` *groupdisplayname* `;` *tenantname*
|Explicit (UPN)  |アプリ   |`aadapp`=*Applicationdisplayname* `;`*TenantId*
|明示的 (名前) |アプリ   |`aadapp=`*ApplicationId* `;`*Tenantname*

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

