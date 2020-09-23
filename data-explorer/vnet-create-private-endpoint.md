---
title: 仮想ネットワーク内の Azure Data Explorer クラスターでプライベート エンドポイントを使用する
description: 仮想ネットワーク内の Azure Data Explorer クラスターでプライベート エンドポイントを使用する
author: orspod
ms.author: orspodek
ms.reviewer: elbirnbo
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: aeb807db9b69c6c5b806a7f4b152330ea2dabc72
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680752"
---
# <a name="create-a-private-endpoint-in-your-azure-data-explorer-cluster-in-your-virtual-network-preview"></a>仮想ネットワーク内の Azure Data Explorer クラスターにプライベート エンドポイントを作成する (プレビュー)

Private Link をプライベート エンドポイントと共に使用して、仮想ネットワーク (VNet) 内の Azure Data Explorer クラスターに安全にアクセスします。 

[Private Link サービス](https://docs.microsoft.com/azure/private-link/private-link-service-overview)を設定するには、Azure VNet アドレス空間の IP アドレスを持つプライベート エンドポイントを使用します。 [Azure プライベート エンドポイント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)は、VNet のプライベート IP アドレスを使用して、Azure Data Explorer にプライベートかつ安全に接続します。 また、プライベート エンドポイントを使用して接続するように、クラスターで [DNS 構成](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)を再構成する必要があります。 この設定により、プライベート ネットワーク上のクライアントと Azure Data Explorer クラスター間のネットワーク トラフィックは、VNet および Microsoft バックボーン ネットワーク上の [Private Link](https://docs.microsoft.com/azure/private-link/) を経由するため、パブリック インターネットにさらされません。 この記事では、クエリ (エンジン) とインジェスト (データ管理) の両方に対して、クラスター内にプライベート エンドポイントを作成して構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [仮想ネットワーク内に Azure Data Explorer クラスター](https://docs.microsoft.com/azure/data-explorer/vnet-create-cluster-portal)を作成します。
* ネットワーク ポリシーを無効にします。
  * Azure Data Explorer クラスターの仮想ネットワークで、[Private Link サービスのポリシー](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)を無効にします。
  * プライベート エンドポイントの仮想ネットワーク (Azure Data Explorer クラスターの仮想ネットワークと同じ場合があります) で、[プライベート エンドポイントのポリシー](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy)を無効にします。

## <a name="create-private-link-service"></a>Private Link サービスの作成

クラスター上のすべてのサービスに安全にリンクするには、[Private Link サービス](https://docs.microsoft.com/azure/private-link/private-link-service-overview) を 2 回作成する必要があります。1 回はクエリ (エンジン) 用で、もう 1 回はインジェスト (データ管理) 用です。

1. ポータルの左上隅にある **[+ リソースの作成]** ボタンを選択します。
1. *Private Link サービス*を検索します。
1. **Private Link サービス**で、 **[作成]** を選択します。

    :::image type="content" source="media/vnet-create-private-endpoint/create-service.gif" alt-text="Azure Data Explorer ポータルで Private Link サービスを作成するための最初の 3 つの手順を示す Gif":::

1. **[Create private link service]\(Private Link サービスの作成\)** ペインで、次のフィールドに入力します。

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-basics.png" alt-text="[Create private link service]\(Private Link サービスの作成\) のタブ 1 - [基本]":::

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | 仮想ネットワーク クラスターが含まれている Azure サブスクリプションを選択します。|
    | Resource group | 該当するリソース グループ | 仮想ネットワーク クラスターが含まれているリソース グループを選択します。 |
    | 名前 | AzureDataExplorerPLS | リソース グループ内で Private Link サービスを識別する名前を選択します。 |
    | リージョン | 仮想ネットワークと同じ | 仮想ネットワークのリージョンに一致するリージョンを選択します。 |

1. **[送信設定]** ペインで、次のフィールドに入力します。

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-outbound.png" alt-text="Private Link のタブ 2 - [送信設定]":::

    |**設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | Load Balancer | エンジンまたは "*データ管理*" のロード バランサー | クラスター エンジンに対して作成されたロード バランサー、エンジンのパブリック IP を指すロード バランサーを選択します。  ロード バランサーのエンジン名は、次の形式になります: kucompute-{clustername}-elb <br> "*ロード バランサーのデータ管理名は、次の形式になります: kudatamgmt-{clustername}-elb*"|
    | ロード バランサーのフロントエンド IP アドレス | エンジンまたはデータ管理のパブリック IP。 | ロード バランサーのパブリック IP アドレスを選択します。 |
    | ソース NAT サブネット | クラスターのサブネット | クラスターがデプロイされているサブネット。
    
1. **[アクセス セキュリティ]** ペインで、Private Link サービスへのアクセスを要求できるユーザーを選択します。
1. **[Review + create]\(確認と作成\)** を選択して、Private Link サービスの構成を確認します。 **[作成]** を選択して、Private Link サービスを作成します。
1. Private Link サービスを作成した後、リソースを開き、次の手順である「**プライベート エンドポイントの作成**」のために、Private Link のエイリアスを保存します。 エイリアスの例:*AzureDataExplorerPLS.111-222-333.westus.azure.privatelinkservice*

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

クラスター上のすべてのサービスに安全にリンクするには、[プライベート エンドポイント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)を 2 回作成する必要があります。1 回はクエリ (エンジン) 用で、もう 1 回はインジェスト (データ管理) 用です。

1. ポータルの左上隅にある **[+ リソースの作成]** ボタンを選択します。
1. "*プライベート エンドポイント*" を検索します。
1. **[プライベート エンドポイント]** で、 **[作成]** を選択します。
1. **[Create a private endpoint]\(プライベート エンドポイントの作成\)** ペインで、次のフィールドに入力します。

    :::image type="content" source="media/vnet-create-private-endpoint/step-one-basics.png" alt-text="プライベート エンドポイントの作成フォーム手順 1 - 基本":::

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | プライベート エンドポイントに使用する Azure サブスクリプションを選択します。|
    | Resource group | 該当するリソース グループ | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 名前 | AzureDataExplorerPE | リソース グループ内で仮想ネットワークを識別する名前を選択します。
    | リージョン | *米国西部* | ニーズに最も適したリージョンを選択します。
    
1. **[リソース]** ペインで、次のフィールドに入力します。

    :::image type="content" source="media/vnet-create-private-endpoint/step-two-resource.png" alt-text="仮想ネットワークの作成フォーム手順 2 - リソース":::

    **設定** | **Value**
    |---|---|
    | 接続方法 | Private Link サービスのエイリアス |
    | リソース ID またはエイリアス | エンジンの Private Link サービスのエイリアス。 エイリアスの例:*AzureDataExplorerPLS.111-222-333.westus.azure.privatelinkservice*  |
    
1. **[Review + create]\(確認と作成\)** を選択して、プライベート エンドポイントの構成を確認し、プライベート エンドポイント サービスを**作成**します。
1. インジェスト (データ管理) 用のプライベート エンドポイントを作成するには、同じ手順を次の変更を加えて実行します。
    1. **[リソース]** ペインで、インジェスト (データ管理) の Private Link サービスの別名を選択します。

> [!NOTE]
> Private Link サービスには、複数のプライベート エンドポイントから接続できます。

## <a name="approve-your-private-endpoint"></a>プライベート エンドポイントの承認

> [!NOTE]
> Private Link サービスを作成するときに **[アクセス セキュリティ]** ウィンドウで "*自動承認*" オプションを選択した場合、この手順は必須ではありません。

1. Private Link サービスの設定で、 **[プライベート エンドポイント接続]** を選択します。
1. 接続の一覧からプライベート エンドポイントを選択し、 **[承認]** を選択します。

:::image type="content" source="media/vnet-create-private-endpoint/private-link-approve.png" alt-text="プライベート エンドポイントを作成するための承認手順"::: 

## <a name="set-dns-configuration"></a>DNS 構成の設定

Azure Data Explorer クラスターを仮想ネットワークにデプロイするときに、レコード名とゾーン ホスト名の間で *privatelink* を使用して正規名をポイントするように [DNS エントリ](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)を更新します。 このエントリは、エンジンとインジェスト (データ管理) の両方に対して更新されます。 

たとえば、エンジンの DNS 名が myadx.westus.kusto.windows.net の場合、名前解決は次のようになります。

* **name**: myadx.westus.kusto.windows.net   <br> **type**: CNAME   <br> **value**: myadx.privatelink.westus.kusto.windows.net
* **name**: myadx.privatelink.westus.kusto.windows.net   <br> **type**: A   <br> **value**:40.122.110.154
    > [!NOTE]
    > この値は、クエリ (エンジン) のパブリック IP アドレスです。これは、クラスターの作成時にユーザーが指定したものです。

プライベート DNS サーバーまたは Azure プライベート DNS ゾーンを設定します。 テストの場合は、テスト マシンのホスト エントリを変更できます。

次の DNS ゾーンを作成します: **privatelink.region.kusto.windows.net**。 この例の DNS ゾーンは次のとおりです: *privatelink.westus.kusto.windows.net*。 A レコードとプライベート エンドポイント IP を使用して、エンジンのレコードを登録します。

たとえば、名前解決は次のようになります。

* **name**: myadx.westus.kusto.windows.net   <br>**type** : CNAME   <br>**value**: myadx.privatelink.westus.kusto.windows.net
* **name**: myadx.privatelink.westus.kusto.windows.net   <br>**type**: A   <br>**value**:10.3.0.9
    > [!NOTE]
    > この値は、プライベート エンドポイント IP です。 既にこの IP をクエリ (エンジン) の Private Link サービスに接続しています。

この DNS 構成を設定した後は、次の URL を使用してプライベートに仮想ネットワーク内のクエリ (エンジン) に接続できます: myadx.region.kusto.windows.net。

インジェスト (データ管理) にプライベートに接続するには、A レコードとインジェストのプライベート エンドポイント IP を使用してインジェスト (データ管理) のレコードを登録します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer クラスターを仮想ネットワークにデプロイする](vnet-deployment.md)
* [仮想ネットワーク内の Azure Data Explorer クラスターのアクセス、インジェスト、操作に関するトラブルシューティング](vnet-deploy-troubleshoot.md)
