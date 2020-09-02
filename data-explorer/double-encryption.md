---
title: Azure Data Explorer でのクラスターの作成中に、インフラストラクチャの暗号化 (二重暗号化) を有効にする
description: この記事では、Azure Data Explorer でのクラスターの作成中に、インフラストラクチャの暗号化 (二重暗号化) を有効にする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/11/2020
ms.openlocfilehash: e89ce6f77545b4f0b42cbb3d792edd5ceeb0ed34
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874675"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>Azure Data Explorer でのクラスターの作成中に、インフラストラクチャの暗号化 (二重暗号化) を有効にする
  
クラスターを作成すると、その記憶域は[サービス レベルで自動的に暗号化](/azure/storage/common/storage-service-encryption)されます。 データがセキュリティで保護されていることのより高いレベルの保証が必要な場合は、[Azure Storage インフラストラクチャ レベルの暗号化](/azure/storage/common/infrastructure-encryption-enable) (二重暗号化とも呼ばれます) を有効にすることもできます。 インフラストラクチャの暗号化が有効な場合、ストレージ アカウントのデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 (サービス レベルで 1 回とインフラストラクチャ レベルで 1 回) 暗号化されます。 Azure Storage データの二重暗号化を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 このシナリオでは、追加の暗号化レイヤーによって引き続きデータが保護されます。

> [!IMPORTANT]
> * 二重暗号化を有効にすることは、クラスターの作成中にしかできません。
> * クラスターでインフラストラクチャの暗号化を有効にした後は、それを無効することは**できません**。
> * 二重暗号化は、インフラストラクチャの暗号化がサポートされているリージョンでのみ使用できます。 詳細については、[ストレージ インフラストラクチャの暗号化](/azure/storage/common/infrastructure-encryption-enable)に関する記事を参照してください。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. [Azure Data Explorer クラスターを作成します](create-cluster-database-portal.md#create-a-cluster) 
1. **[セキュリティ]** タブの **[Enable Double Encryption]\(二重暗号化を有効にする\)** で、 **[オン]** を選択します。 二重暗号化を解除するには、 **[オフ]** を選択します。
1. **[次へ: ネットワーク >]** または **[確認と作成]** を選択してクラスターを作成します。

    :::image type="content" source="media/double-encryption/double-encryption-portal.png" alt-text="新しいクラスターでの二重暗号化":::


# <a name="c"></a>[C#](#tab/c-sharp)

C# を使用して、クラスターの作成中にインフラストラクチャの暗号化を有効にすることができます。

## <a name="prerequisites"></a>前提条件

Azure Data Explorer C# クライアントを使用してマネージド ID を設定します。

* [Azure Data Explorer (Kusto) NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)をインストールします。
* 認証用に、[Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)をインストールします。
* リソースにアクセスできる [Azure AD アプリケーション](/azure/active-directory/develop/howto-create-service-principal-portal)とサービス プリンシパルを作成します。 サブスクリプションのスコープでロールの割り当てを追加し、必要な `Directory (tenant) ID`、`Application ID`、および `Client Secret` を取得します。

## <a name="create-your-cluster"></a>クラスターの作成

1. `enableDoubleEncryption` プロパティを使用してクラスターを作成します。

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
                                                                                                    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "East US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var enableDoubleEncryption = true;
    var cluster = new Cluster(location, sku, enableDoubleEncryption: enableDoubleEncryption);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
1. 次のコマンドを実行して、クラスターが正常に作成されたかどうかを確認します。

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    結果に値が `Succeeded` の `ProvisioningState` が含まれている場合、クラスターは正常に作成されています。

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm)

Azure Resource Manager を使用して、クラスターの作成中にインフラストラクチャの暗号化を有効にすることができます。

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 Azure Data Explorer へのデプロイの詳細については、「[Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-resource-manager.md)」を参照してください。

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して、システム割り当て ID を追加する

1. 'EnableDoubleEncryption' 型を追加して、クラスターのインフラストラクチャの暗号化 (二重暗号化) を有効にするように Azure に指示します。
    
    ```json
    {
        "apiVersion": "2020-06-14",
        "type": "Microsoft.Kusto/clusters",
        "name": "[variables('clusterName')]",
        "location": "[resourceGroup().location]",
        "properties": {
            "trustedExternalTenants": [],
            "virtualNetworkConfiguration": null,
            "optimizedAutoscale": null,
            "enableDiskEncryption": false,
            "enableStreamingIngest": false,
            "enableDoubleEncryption": true,
        }
    }
    ```

1. クラスターが作成されると、次の追加プロパティを持ちます。

    ```json
    "identity": {
        "type": "SystemAssigned",
        "tenantId": "<TENANTID>",
        "principalId": "<PRINCIPALID>"
    }
    ```
---

## <a name="next-steps"></a>次のステップ

[クラスターの正常性を確認する](check-cluster-health.md)
