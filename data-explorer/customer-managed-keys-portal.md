---
title: Azure portal を使用してカスタマー マネージド キーを構成する
description: この記事では、Azure Data Explorer でデータに対するカスタマー マネージド キーの暗号化を構成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 4d828905ce673c3b1c6453d31974334312bd3b5e
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872150"
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

    ![顧客管理キーの構成](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. **[Azure Key Vault からのキーの選択]** ウィンドウで、ドロップダウン リストから既存の **Key Vault** を選択します。 **[新規作成]** を選択して[新しい Key Vault を作成する](/azure/key-vault/quick-create-portal#create-a-vault)場合は、 **[Key Vault の作成]** 画面にルーティングされます。

1. **[キー]** を選択します。
1. **[バージョン]** を選択します。
1. **[選択]** をクリックします。

    ![Azure Key Vault からキーを選択する](media/customer-managed-keys-portal/cmk-key-vault.png)

1. キーが含まれている **[暗号化]** ウィンドウで、 **[保存]** を選択します。 CMK の作成に成功すると、 **[通知]** に成功メッセージが表示されます。

    ![カスタマー マネージド キーを保存する](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Azure Data Explorer クラスターに対してカスタマー マネージド キーを有効にしたら、システムによって割り当てられた ID がクラスターに存在しない場合は ID を作成します。 また、選択した Key Vault の Azure Data Explorer クラスターに対して必要な get、wrapKey、unwarpKey の各アクセス許可を提供し、Key Vault のプロパティを取得します。 

> [!NOTE]
> カスタマー マネージド キーを作成した後に削除するには、 **[オフ]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Azure で Azure Data Explorer クラスターをセキュリティで保護する](security.md)
* 保存時の暗号化を有効にすることで、[Azure Data Explorer のディスク暗号化を使用してクラスターをセキュリティで保護する - Azure portal](cluster-disk-encryption.md)。
* [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](customer-managed-keys-resource-manager.md)
* [C# を使用してカスタマー マネージド キーを構成する](customer-managed-keys-csharp.md)



