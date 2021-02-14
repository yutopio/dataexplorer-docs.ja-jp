---
title: PowerShell を使用して Azure Data Explorer クラスターと DB を作成する
description: PowerShell を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 807b2d4a6ee5663364196f3c2c751f564b2bb80d
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554748"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>PowerShell を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM テンプレート](create-cluster-database-resource-manager.md)  

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 この記事では、PowerShell を使用して、クラスターとデータベースを 1 つずつ作成します。 [Az.Kusto](/powershell/module/az.kusto/#kusto) を使って Windows、Linux、または [Azure Cloud Shell](/azure/cloud-shell/overview) で PowerShell コマンドレットとスクリプトを実行し、Azure Data Explorer のクラスターとデータベースを作成および構成できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](includes/updated-for-az.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="configure-parameters"></a>パラメーターを構成する

Azure Cloud Shell でコマンドを実行している場合、次の手順は必要ありません。 ローカルで CLI を実行している場合、手順 1、2 に従って Azure にサインインし、現在のサブスクリプションを設定します。

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. クラスターを作成するサブスクリプションを設定します。

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. ローカルまたは Azure Cloud Shell で Azure CLI を実行している場合、ご利用のドライブに Az.Kusto モジュールをインストールする必要があります。

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコマンドを使用して、クラスターを作成します。

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | 名前 | *mykustocluster* | クラスターの任意の名前。|
   | Sku | *D13_v2* | クラスターに使用される SKU。 |
   | ResourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。

1. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

結果に値が `provisioningState` の `Succeeded` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコマンドを使用して、データベースを作成します。

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | ClusterName | *mykustocluster* | データベースの作成先となるクラスターの名前。|
   | 名前 | *mykustodatabase* | データベースの名前。|
   | ResourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | SoftDeletePeriod | *3650:00:00:00* | データをクエリに使用できるようにしておく時間。 |
   | HotCachePeriod | *3650:00:00:00* | データをキャッシュに保持する時間。 |

1. 次のコマンドを実行して、作成したデータベースを確認します。

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

* 他の記事に進む場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>次のステップ

* [その他の Az.Kusto コマンド](/powershell/module/az.kusto/#kusto)
* [Azure Data Explorer .NET Standard SDK (プレビュー) を使用してデータを取り込む](./net-sdk-ingest-data.md)
