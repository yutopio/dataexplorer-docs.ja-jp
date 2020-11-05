---
title: イベント ハブや Azure Storage など、データ接続で使用されるリソースへのプライベートまたはサービス エンドポイントを作成する
description: イベント ハブや Storage など、データ接続で使用されるリソースへのプライベートまたはサービス エンドポイントを作成する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: gunjand
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: 43f7705170228afa3d3f5e31086d40cea73c62db
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906223"
---
# <a name="create-a-private-or-service-endpoint-to-event-hub-and-azure-storage"></a>イベントハブと Azure Storage へのプライベートまたはサービス エンドポイントを作成する

[Azure Virtual Network (VNet)](/azure/virtual-network/virtual-networks-overview) を使用すると、さまざまな種類の Azure リソースが安全に相互に通信できるようになります。 [Azure Private Link](/azure/private-link/) を使用すると、Azure サービスと Azure でホストされている顧客所有またはパートナーのサービスに、仮想ネットワーク内のプライベート エンドポイント経由でアクセスできます。 [プライベート エンドポイント](/azure/private-link/private-endpoint-overview)では、Azure サービスに対して VNet のアドレス空間からの IP アドレスが使用されて、Azure Data Explorer と、Azure Storage やイベント ハブなどの Azure サービス間の安全な接続が行われます。 Azure Data Explorer では、Microsoft バックボーン経由でストレージ アカウントや Event Hubs のプライベート エンドポイントへのアクセスが行われます。また、データのエクスポート、外部テーブル、データ インジェストなどのすべての通信は、プライベート IP アドレスを介して行われます。 

プライベート エンドポイントとは異なり、[サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)は、パブリックにルーティング可能な IP アドレスのままです。 仮想ネットワーク (VNet) サービス エンドポイントでは、Azure のバックボーン ネットワーク上で最適化されたルートを介して、Azure サービスに安全に直接接続できます。 

この記事では、Azure Data Explorer と[イベント ハブ](ingest-data-event-hub-overview.md)または [Azure Storage](/azure/storage/) 間の接続を作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
* [Azure Data Explorer サブネット](vnet-deployment.md)

## <a name="private-endpoint"></a>プライベート エンドポイント

Azure [プライベート エンドポイント](/azure/private-link/private-endpoint-overview)は、Azure [Private Link](/azure/private-link/) を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 
 
# <a name="azure-storage---private-endpoint"></a>[Azure Storage - プライベート エンドポイント](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>プライベート エンドポイントを使用して Azure Data Explorer サブネットから Azure Storage アカウントにアクセスできるようにする

Azure Storage アカウントでプライベート エンドポイントを作成する方法のチュートリアルについては、「[チュートリアル: Azure プライベート エンドポイントを使用してストレージ アカウントに接続する](/azure/private-link/tutorial-private-endpoint-storage-portal)」を参照してください。

このチュートリアルでは、Azure Data Explorer サブネットが存在する VNet と、Azure Data Explorer サブネットを選択します。

# <a name="event-hub---private-endpoint"></a>[イベント ハブ - プライベート エンドポイント](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>プライベート エンドポイントを使用して Azure Data Explorer サブネットからイベント ハブにアクセスできるようにする

イベント ハブでプライベート エンドポイントを作成する方法のチュートリアルについては、「[Azure portal を使用してプライベート エンドポイントを追加する](/azure/event-hubs/private-link-service#add-a-private-endpoint-using-azure-portal)」を参照してください。

このチュートリアルでは、Azure Data Explorer サブネットが存在する VNet と、Azure Data Explorer サブネットを選択します。

---

## <a name="service-endpoint"></a>サービス エンドポイント

仮想ネットワーク (VNet) [サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)では、Azure のバックボーン ネットワーク上で最適化されたルートを介して、Azure サービスに安全に直接接続できます。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 

# <a name="azure-storage---service-endpoint"></a>[Azure Storage - サービス エンドポイント](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>サービス エンドポイントを使用して Azure Data Explorer サブネットから Azure Storage アカウントにアクセスできるようにする

このセクションでは、Azure portal を使用して仮想ネットワーク サービス エンドポイントを追加する方法を示します。 アクセスを制限するには、この Azure Storage アカウントに対して仮想ネットワーク サービス エンドポイントを統合します。

### <a name="add-a-virtual-network"></a>仮想ネットワークの追加 

1. セキュリティで保護するストレージ アカウントを表示します。
1. 左側のメニューから、 **[ファイアウォールと仮想ネットワーク]** を選択します。
1. **[選択されたネットワーク]** からのアクセスを有効にします。
1. **[仮想ネットワーク]** で、 **[+ 既存の仮想ネットワークを追加]** を選択します。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-existing-vnet.png" alt-text="既存の VNet 接続 Azure Storage を Azure Data Explorer に追加する":::

### <a name="add-networks-pane"></a>[ネットワークを追加する] ペイン

:::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-networks.png" alt-text="仮想ネットワークを Azure Storage Account に追加して、Azure Data Explorer に接続する":::

1. 右側の **[ネットワークを追加する]** ペインで、Azure サブスクリプションを選択します。

1. 仮想ネットワークの一覧から仮想ネットワークを選択し、サブネットを選択します。 

    > [!NOTE]
    > 仮想ネットワークを一覧に追加する前に、サービス エンドポイントを有効にします。 サービス エンドポイントが有効になっていない場合は、有効にするように求められます。
    
1. **[追加]** を選択します。

### <a name="save-and-verify-virtual-network-settings"></a>仮想ネットワークの設定を保存して確認する

1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-virtual-network.png" alt-text="ストレージ アカウントを Azure Data Explorer に接続する VNet":::

    ポータルの通知に確認が表示されるまで、数分間待ちます。

# <a name="event-hub---service-endpoint"></a>[イベント ハブ - サービス エンドポイント](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>サービス エンドポイントを使用して Azure Data Explorer サブネットからイベント ハブにアクセスできるようにする

> [!IMPORTANT]
> 仮想ネットワークは、 **Standard** と **Dedicated** レベルの Event Hubs でサポートされています。Basic レベルではサポートされていません。 

### <a name="add-a-virtual-network"></a>仮想ネットワークの追加

1. Azure portal で、セキュリティで保護する **Event Hubs 名前空間** に移動します。
1. 左側のメニューで、 **[ネットワーク]** を選択します。 このタブは、 **Standard** または **Dedicated** の名前空間にのみ表示されます。
1. **[ファイアウォールと仮想ネットワーク]** タブを選択します。

    :::image type="content" source="media/vnet-private-link-storage-event-hub/networking.png" alt-text="イベント ハブでのネットワーク":::

1. **[選択されたネットワーク]** からのアクセスを有効にします。
1. **[仮想ネットワーク]** で、 **[+ 既存の仮想ネットワークを追加]** を選択します。 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-add-existing-vnet.png" alt-text="Azure Data Explorer に既存の仮想ネットワークを追加する":::

### <a name="add-networks-pane"></a>[ネットワークを追加する] ペイン

:::image type="content" source="media/vnet-private-link-storage-event-hub/add-networks.png" alt-text="VNet を Azure Data Explorer に接続するための [ネットワークを追加する] のフィールド":::  

1. 右側の **[ネットワークを追加する]** ペインで、Azure サブスクリプションを選択します。

1. 仮想ネットワークの一覧から仮想ネットワークを選択し、サブネットを選択します。 仮想ネットワークを一覧に追加する前に、サービス エンドポイントを有効にする必要があります。 
    > [!NOTE]
    > 仮想ネットワークを一覧に追加する前に、サービス エンドポイントを有効にします。 サービス エンドポイントが有効になっていない場合は、有効にするように求められます。
1. **[追加]** を選択します。

### <a name="save-and-verify-virtual-network-settings"></a>仮想ネットワークの設定を保存して確認する

1. ツール バーの **[保存]** を選択して設定を保存します。 ポータルの通知に確認が表示されるまで、数分間待ちます。
    
    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-firewalls-and-vnet.png" alt-text="イベント ハブで仮想ネットワークとサブネットを追加して、Azure Data Explorer に接続する"::: 

---

## <a name="next-steps"></a>次のステップ

* [データをストレージにエクスポートする](kusto/management/data-export/export-data-to-storage.md)
* [イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md)
