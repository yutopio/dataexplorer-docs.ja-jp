---
title: Azure portal を使用してカスタマー マネージド キーを構成する
description: Azure portal を使用して Azure Data Explorer データを暗号化するために、カスタマー マネージド キーを構成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: dc94b3712b51336b8ef069329a2f1aa48aae0bb2
ms.sourcegitcommit: ab318bcb8f40c9d10395d1dc3e0cf7a1be1a549f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625567"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Azure portal を使用してカスタマー マネージド キーを構成する

> [!div class="op_single_selector"]
> * [ポータル](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager テンプレート](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Azure portal でカスタマー マネージド キーによる暗号化を有効にする

この記事では、Azure portal を使用してカスタマー マネージド キーによる暗号化を有効にする方法について説明します。 既定では、Azure Data Explorer の暗号化では Microsoft のマネージド キーが使用されます。 カスタマー マネージド キーを使用するように Azure Data Explorer クラスターを構成し、そのクラスターに関連付けるキーを指定します。

1. [Azure portal](https://portal.azure.com/) で、[Azure Data Explorer クラスター](create-cluster-database-portal.md#create-a-cluster) リソースに移動します。
1. portal の左側のウィンドウで、 **[設定]**  >  **[暗号化]** を選択します。
1. **[暗号化]** ウィンドウの **[顧客が管理するキー]** 設定で、 **[オン]** を選択します。
1. **[キーを選択します]** をクリックします。

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-encryption-setting.png" alt-text="顧客管理キーの構成":::

1. **[Azure Key Vault からのキーの選択]** ウィンドウで、ドロップダウン リストから既存の **Key Vault** を選択します。 **[新規作成]** を選択して [新しい Key Vault を作成する](/azure/key-vault/quick-create-portal#create-a-vault)場合は、 **[Key Vault の作成]** 画面にルーティングされます。

1. **[キー]** を選択します。
1. バージョン:
    * このキーで常に最新のキー バージョンが使用されるようにするには、 **[Always use current key version]\(常に現在のキー バージョンを使用する\)** チェックボックスをオンにします。
    * それ以外の場合は、 **[バージョン]** を選択します。
1. **[選択]** をクリックします。

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-key-vault.png" alt-text="Azure Key Vault からキーを選択する":::

1. **[ID の種類]** で、 **[システム割り当て済み]** または **[ユーザー割り当て済み]** を選択します。
1. **[ユーザー割り当て済み]** を選択する場合は、ドロップダウンからユーザー割り当て ID を選択します。

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-select-user-type.png" alt-text="マネージド ID の種類を選択":::

1. キーが含まれている **[暗号化]** ウィンドウで、 **[保存]** を選択します。 CMK の作成に成功すると、 **[通知]** に成功メッセージが表示されます。

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-before-save.png" alt-text="カスタマー マネージド キーを保存する":::

Azure Data Explorer クラスターに対してカスタマー マネージド キーを有効にするときにシステム割り当て ID を選択した場合、システム割り当て ID がクラスターに存在しない場合は ID を作成します。 また、選択した Key Vault の Azure Data Explorer クラスターに対して必要な get、wrapKey、unwarpKey の各アクセス許可を提供し、Key Vault のプロパティを取得します。

> [!NOTE]
> カスタマー マネージド キーを作成した後に削除するには、 **[オフ]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Azure で Azure Data Explorer クラスターをセキュリティで保護する](security.md)
* 保存時の暗号化を有効にすることで、[Azure Data Explorer のディスク暗号化を使用してクラスターをセキュリティで保護する - Azure portal](cluster-disk-encryption.md)。
* [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](customer-managed-keys-resource-manager.md)
* [C# を使用してカスタマー マネージド キーを構成する](customer-managed-keys-csharp.md)
