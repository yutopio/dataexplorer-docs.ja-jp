---
title: Azure Data Explorer クラスターで分離コンピューティングを有効にする
description: この記事では、適切な SKU を選択して、Azure Data Explorer クラスターで分離コンピューティングを有効にする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: dagrawal
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.custom: references_regions
ms.openlocfilehash: 8139701037a8d4d25490a355cc822051851c85cd
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682293"
---
# <a name="enable-isolated-compute-on-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターで分離コンピューティングを有効にする

分離コンピューティング仮想マシン (VM) を使用すると、お客様は、単一のお客様専用のハードウェア分離環境でワークロードを実行できます。 分離コンピューティング VM でデプロイされたクラスターは、コンプライアンスと規制の要件に対して高いレベルの分離を必要とするワークロードに最適です。 コンピューティング SKU は、構成の柔軟性を損なうことなく、データをセキュリティで保護するための分離を提供します。 詳細については、「[コンピューティングの分離](/azure/security/fundamentals/isolation-choices#compute-isolation)」と[分離コンピューティングのための Azure ガイダンス](/azure/azure-government/azure-secure-isolation-guidance#compute-isolation)に関する記事を参照してください。 

Azure Data Explorer は、SKU **Standard_E64i_v3**を使用した分離コンピューティングをサポートします。 この SKU は、アプリケーションまたはエンタープライズのニーズに合わせて自動的にスケールアップおよびスケールダウンできます。 分離コンピューティングのサポートは、次のリージョンで利用できます。
* 米国西部 2
* 米国東部 
* 米国中南部

分離コンピューティング VM は、高価ですが、サーバー インスタンス レベルの分離を必要とするワークロードを実行するのに理想的な SKU です。 Azure Data Explorer でサポートされている SKU の詳細については、「[Azure Data Explorer クラスターに適した VM SKU を選択する](manage-cluster-choose-sku.md)」を参照してください。

> [!NOTE]
> [Azure Dedicated Host](https://azure.microsoft.com/services/virtual-machines/dedicated-host/) は、現在 Azure Data Explorer ではサポートされていません。 

## <a name="enable-isolated-compute-on-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターで分離コンピューティングを有効にする 

Azure Data Explorer クラスターで分離コンピューティングを有効にするには、以下のいずれかのプロセスを実行します。
* [分離コンピューティング SKU でクラスターを作成する](#create-a-cluster-with-isolated-compute-sku)
* [既存のクラスターで分離コンピューティング SKU を選択する](#select-the-isolated-compute-sku-on-an-existing-cluster)

## <a name="create-a-cluster-with-isolated-compute-sku"></a>分離コンピューティング SKU でクラスターを作成する

1. 手順に従って、[Azure portal でAzure Data Explorer のクラスターとデータベースを作成します](create-cluster-database-portal.md)。
1. [クラスターの作成](create-cluster-database-portal.md#create-a-cluster)で、 **[基本]** タブ内の **[Compute specifications]\(コンピューティングの仕様\)** ドロップダウンで **[Standard_E64i_v3]** を選択します。

## <a name="select-the-isolated-compute-sku-on-an-existing-cluster"></a>既存のクラスターで分離コンピューティング SKU を選択する

1. Azure Data Explorer クラスター **[概要]** 画面で、 **[スケールアップ]** を選択します。
1. 検索ボックスで *[Standard_E64i_v3]* を検索し、SKU 名をクリックするか、SKU リストから **[Standard_E64i_v3]** の SKU を選択します。
1. **[適用]** を選択して、お使いの SKU を更新します。 

:::image type="content" source="media/isolated-compute/select-isolated-compute-sku.png" alt-text="分離コンピューティング SKU を選択する":::

> [!TIP]
> スケールアップ プロセスには数分かかる場合があります。

## <a name="next-steps"></a>次のステップ

* [需要の変化に対応するために Azure Data Explorer のクラスターの垂直スケーリング (スケールアップ) を管理する](manage-cluster-vertical-scaling.md)
* [Azure Data Explorer クラスターに適した VM SKU を選択する](manage-cluster-choose-sku.md)
