---
title: Azure で Azure Data Explorer クラスターをセキュリティで保護する
description: Azure Data Explorer のクラスターをセキュリティで保護する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 4bf068b03ab9752440f2bf6d05a57022db9dbbb2
ms.sourcegitcommit: d77e52909001f885d14c4d421098a2c492b8c8ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772350"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Azure で Azure Data Explorer クラスターをセキュリティで保護する

この記事では、クラウド内のデータとリソースを保護し、ビジネスのセキュリティ ニーズを満たすのに役立つ、Azure Data Explorer のセキュリティの概要を示します。 クラスターをセキュリティで保護することが重要です。 クラスターのセキュリティ保護には、セキュリティで保護されたアクセスとストレージを含む 1 つまたは複数の Azure 機能があります。 この記事では、クラスターをセキュリティで保護するために役立つ情報を提供します。

## <a name="managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID

クラウド アプリケーションのビルド時における一般的な課題は、コードでのクラウド サービスに対する認証用の資格情報の管理です。 資格情報を安全に保つことは重要な課題です。 資格情報は、開発者のワークステーションに格納したり、ソース管理にチェックインしたりすべきではありません。 資格情報やシークレットなど、各種キーを安全に保管する手段としては Azure Key Vault がありますが、それらを取得するためには、コードから Key Vault に対して認証を行わなければなりません。

この問題は、Azure Active Directory (Azure AD) の Azure リソースのマネージド ID 機能によって解決されます。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。 このサービスの詳細については、[Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview) の概要ページを確認してください。

## <a name="data-encryption"></a>データの暗号化

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 クラスターの仮想マシンの OS とデータ ディスクのボリューム暗号化が提供されます。 Azure Disk Encryption は [Azure Key Vault](/azure/key-vault/) とも統合されます。これにより、ディスク暗号化キーとシークレットの制御と管理を行って、VM ディスク上の全データを確実に暗号化することができます。 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault でのカスタマー マネージド キー

規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、データの暗号化に使用する目的で、カスタマー マネージド キーを提供できます。 独自のキーを使用して、ストレージ レベルでデータの暗号化を管理できます。 カスタマー マネージド キーは、すべてのデータの暗号化と暗号化解除に使用されるルート暗号化キーの保護とアクセス制御を行うために使用されます。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

カスタマー マネージド キーを格納するには、Azure Key Vault を使用します。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Data Explorer クラスターと Azure キー コンテナーは同じリージョンに存在している必要があります。ただし、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](/azure/key-vault/key-vault-overview)」をご覧ください。 カスタマー マネージド キーの詳細については、[カスタマー マネージド キーと Azure Key Vault](/azure/storage/common/storage-service-encryption) に関するページを参照してください。 [ポータル](customer-managed-keys-portal.md)、[C#](customer-managed-keys-csharp.md)、[Azure Resource Manager テンプレート](customer-managed-keys-resource-manager.md)、CLI、または [PowerShell](customer-managed-keys-powershell.md) を使用して、Azure Data Explorer クラスターでカスタマー マネージド キーを構成します。

> [!Note]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 Azure portal でカスタマー マネージド キーを構成するには、「[Azure Data Explorer クラスターのマネージド ID の構成](managed-identities.md)」の説明に従って、クラスターに対してマネージド ID を構成します。

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault にカスタマー マネージド キーを格納する

クラスターのカスタマー マネージド キーを有効にするには、Azure キー コンテナーを使用してキーを格納する必要があります。 Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にする必要があります。 キー コンテナーはクラスターと同じサブスクリプションに配置する必要があります。 Azure Data Explorer では、暗号化操作と暗号化解除操作のために、Azure リソースのマネージド ID を使用してキー コンテナーに対する認証を行います。 マネージド ID では、クロスディレクトリ シナリオはサポートされていません。

#### <a name="rotate-customer-managed-keys"></a>カスタマー マネージド キーをローテーションする  

Azure Key Vault でキーのバージョンを更新するか、新しいキーを作成してから、そのキーを使用してデータを暗号化するようにクラスターを更新します。 これらの手順は、Azure CLI またはポータルを使用して実行できます。 キーをローテーションしても、クラスターでのデータの再暗号化はトリガーされません。

キーをローテーションする場合、通常はクラスターの作成時に使用したものと同じ ID を指定します。 必要に応じて、キー アクセス用に新しいユーザー割り当て ID を構成するか、クラスターのシステム割り当て ID を有効にして指定します。

> [!NOTE]
> キー アクセス用に構成する ID に対して、必要な **[取得]** 、 **[キーの折り返しを解除]** 、 **[キーを折り返す]** アクセス許可が設定されていることを確認します。

##### <a name="update-key-version"></a>キーのバージョンを更新する

一般的なシナリオでは、カスタマー マネージド キーとして使用されるキーのバージョンを更新します。 クラスターの暗号化の構成方法に応じて、クラスターのカスタマー マネージド キーは、自動的に更新されるか、または手動で更新する必要があります。

#### <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](/powershell/module/az.keyvault/) に関する記事、または [Azure Key Vault CLI](/cli/azure/keyvault) に関する記事を参照してください。 アクセスを取り消すと、すべてのデータへのアクセスがクラスターのストレージ レベルでブロックされます。これは、Azure Data Explorer による暗号化キーのアクセスが不可能になるためです。

> [!Note]
> カスタマー マネージド キーへのアクセスが取り消されたことが Azure Data Explorer で識別されると、キャッシュされたデータを削除するためにクラスターが自動的に停止されます。 キーへのアクセスが回復すると、クラスターは自動的に再開されます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

[ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) を使用して、チーム内で職務を分離し、必要なアクセス許可のみをクラスターのユーザーに付与することができます。 すべてのユーザーにクラスターへの無制限のアクセス許可を付与するのではなく、特定の操作のみを許可することができます。 [データベースに対するアクセス制御](manage-database-permissions.md)は、[Azure portal](/azure/role-based-access-control/role-assignments-portal) で構成できるほか、[Azure CLI](/azure/role-based-access-control/role-assignments-cli) または [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell) を使って構成することもできます。

## <a name="next-steps"></a>次のステップ

* 保存時の暗号化を有効にすることで、[Azure Data Explorer のディスク暗号化を使用してをクラスターをセキュリティで保護する - ポータル](cluster-disk-encryption.md)
* [Azure Data Explorer クラスターのマネージド ID を構成する](managed-identities.md)
* [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](customer-managed-keys-resource-manager.md)
* [C# を使用してカスタマー マネージド キーを構成する](customer-managed-keys-csharp.md)
