---
title: フォロワー データベース機能を使用して Azure Data Explorer にデータベースをアタッチする
description: フォロワー データベース機能を使用して Azure Data Explorer にデータベースをアタッチする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/06/2020
ms.openlocfilehash: 218d4962c1b7317f4ed94fb86c576da336ba93ee
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528124"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>フォロワー データベースを使用して Azure Data Explorer にデータベースをアタッチする

**フォロワー データベース** 機能を使うと、別のクラスターにあるデータベースを Azure Data Explorer クラスターにアタッチできます。 **フォロワー データベース** は "*読み取り専用*" モードでアタッチされるため、データを表示したり、**リーダー データベース** に取り込まれたデータに対してクエリを実行したりできます。 フォロワー データベースには、リーダー データベースの変更が同期されます。 この同期により、データが利用可能になるまでに数秒から数分のデータ遅延が発生します。 遅延の長さは、リーダー データベースのメタデータ全体のサイズに応じて異なります。 リーダー データベースとフォロワー データベースでは、データをフェッチするために同じストレージ アカウントが使用されます。 ストレージを所有するのはリーダー データベースです。 フォロワー データベースでは、データを取り込むことなくデータを表示できます。 アタッチされたデータベースは読み取り専用のデータベースであるため、データベース内のデータ、テーブル、およびポリシーの変更はできませんが、[キャッシュ ポリシー](#configure-caching-policy)、[プリンシパル](#manage-principals)、および[アクセス許可](#manage-permissions)の変更は可能です。 アタッチされたデータベースは削除できません。 これらは、リーダーまたはフォロワーによってデタッチされた後にのみ、削除できます。 

フォロワー機能を使った別のクラスターへのデータベースのアタッチは、組織とチームの間でデータを共有するためのインフラストラクチャとして使用されます。 この機能は、非運用環境のユース ケースから運用環境を保護するためにコンピューティング リソースを分離するのに役立ちます。 また、フォロワーを使うことで、Azure Data Explorer クラスターのコストを、そのデータに対してクエリを実行するパーティーに関連付けることができます。

## <a name="which-databases-are-followed"></a>フォロー対象データベース

* クラスターでは、1 つのデータベース、複数のデータベース、またはリーダー クラスターのすべてのデータベースをフォローできます。 
* 単一のクラスターでは、複数のリーダー クラスターのデータベースをフォローできます。 
* クラスターには、フォロワー データベースとリーダー データベースの両方を含めることができます。
* EngineV3 クラスターがフォローできるのは EngineV3 クラスターのみです。同様に、EngineV2 クラスターがフォローできるのは V2 クラスターのみです。

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
1. リーダーとフォロワー用の[クラスターと DB を作成](create-cluster-database-portal.md)します。
1. 「[データ取り込みの概要](./ingest-data-overview.md)」に記載されているさまざまな方法の 1 つを使用して、リーダー データベースに[データを取り込み](ingest-sample-data.md)ます。

## <a name="attach-a-database"></a>データベースのアタッチ

データベースのアタッチにはさまざまな方法があります。 この記事では、C#、Python、PowerShell、または Azure Resource Manager テンプレートを使用してデータベースをアタッチする方法について説明します。 データベースをアタッチするには、リーダー クラスターとフォロワー クラスターに対して少なくとも共同作成者ロールを持つユーザー、グループ、サービス プリンシパル、またはマネージド ID が必要です。 [Azure portal](/azure/role-based-access-control/role-assignments-portal)、[PowerShell](/azure/role-based-access-control/role-assignments-powershell)、[Azure CLI](/azure/role-based-access-control/role-assignments-cli)、[ARM テンプレート](/azure/role-based-access-control/role-assignments-template)を使用して、ロールの割り当てを追加または削除できます。 詳細については、「[Azure のロールベースのアクセス制御 (Azure RBAC)](/azure/role-based-access-control/overview)」および[各種ロール](/azure/role-based-access-control/rbac-and-directory-admin-roles)に関する記事をご覧ください。 


# <a name="c"></a>[C#](#tab/csharp)

### <a name="attach-a-database-using-c"></a>C# を使用したデータベースのアタッチ

#### <a name="needed-nugets"></a>必要な NuGet

* [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/) をインストールします。
* [認証用の Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) をインストールします。

#### <a name="example"></a>例

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

# <a name="python"></a>[Python](#tab/python)

### <a name="attach-a-database-using-python"></a>Python を使用したデータベースのアタッチ

#### <a name="needed-modules"></a>必要なモジュール

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="example"></a>例

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="attach-a-database-using-powershell"></a>PowerShell を使用してデータベースをアタッチする

#### <a name="needed-modules"></a>必要なモジュール

```
Install : Az.Kusto
```

#### <a name="example"></a>例

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "db"  ## Can be specific database name or * for all databases
$LeaderClustername = 'leader'
$LeaderClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$LeaderClusterResourceGroup = 'leaderResouceGroup'
$DefaultPrincipalsModificationKind = 'Union'
##Construct the LeaderClusterResourceId and Location
$getleadercluster = Get-AzKustoCluster -Name $LeaderClustername -ResourceGroupName $LeaderClusterResourceGroup -SubscriptionId $LeaderClusterSubscriptionID -ErrorAction Stop
$LeaderClusterResourceid = $getleadercluster.Id
$Location = $getleadercluster.Location
##Handle the config name if all databases needs to be followed
if($DatabaseName -eq '*')  {
        $configname = $FollowerClustername + 'config'
       } 
else {
        $configname = $DatabaseName   
     }
New-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername `
    -Name $configname `
    -ResourceGroupName $FollowerResourceGroupName `
    -SubscriptionId $FollowerClusterSubscriptionID `
    -DatabaseName $DatabaseName `
    -ClusterResourceId $LeaderClusterResourceid `
    -DefaultPrincipalsModificationKind $DefaultPrincipalsModificationKind `
    -Location $Location `
    -ErrorAction Stop 
```

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用したデータベースのアタッチ

このセクションでは、[Azure Resource Manager テンプレート](/azure/azure-resource-manager/management/overview)を使用してデータベースを既存のクラスターにアタッチする方法について説明します。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2020-02-15",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>テンプレートのデプロイ 

[Azure portal を使用して](https://portal.azure.com)、または PowerShell を使用して、Azure Resource Manager テンプレートをデプロイできます。

   ![テンプレートのデプロイ](media/follower/template-deployment.png)

|**設定**  |**説明**  |
|---------|---------|
|Follower Cluster Name (フォロワー クラスター名)     |  テンプレートを配置するフォロワー クラスターの名前。  |
|Attached Database Configurations Name (アタッチされたデータベースの構成名)    |    アタッチされたデータベースの構成オブジェクトの名前。 クラスター レベルで一意であれば、名前には任意の文字列を指定できます。     |
|データベース名     |      フォローするデータベースの名前。 リーダーのデータベースをすべてフォローする場合は、'*' を使用します。   |
|Leader Cluster Resource ID (リーダー クラスターのリソース ID)    |   リーダー クラスターのリソース ID。      |
|Default Principals Modification Kind (既定のプリンシパル変更の種類)    |   既定のプリンシパル変更の種類。 `Union`、`Replace`、または `None` を指定できます。 既定のプリンシパル変更の種類について詳しくは、「[プリンシパル変更の種類の管理コマンド](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind)」をご覧ください。      |
|場所   |   すべてのリソースの場所。 リーダーとフォロワーは同じ場所にある必要があります。       |

---

## <a name="verify-that-the-database-was-successfully-attached"></a>データベースが正常にアタッチされたことを確認する

データベースが正常にアタッチされたことを確認するには、[Azure portal](https://portal.azure.com) でアタッチされたデータベースを見つけます。 [フォロワー](#check-your-follower-cluster)または[リーダー](#check-your-leader-cluster) のクラスターでデータベースが正常にアタッチされたことを確認できます。

### <a name="check-your-follower-cluster"></a>フォロワー クラスターを確認する  

1. フォロワー クラスターに移動し、 **[データベース]** を選択します。
1. データベースの一覧で、新しい読み取り専用データベースを検索します。

    ![読み取り専用のフォロワー データベース](media/follower/read-only-follower-database.png)

### <a name="check-your-leader-cluster"></a>リーダー クラスターを確認する

1. リーダー クラスターに移動し、 **[データベース]** を選択します。
2. 関連するデータベースの **[他のユーザーと共有]**  >  が **[はい]** に設定されていることを確認します。

    ![アタッチされたデータベースの読み取りと書き込み](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database"></a>フォロワー データベースをデタッチする  

> [!NOTE]
> フォロワーまたはリーダー側からデータベースをデタッチするには、データベースをデタッチするクラスターに対して少なくとも共同作成者ロールを持つユーザー、グループ、サービス プリンシパル、またはマネージド ID が必要です。 下の例では、サービス プリンシパルを使用します。

# <a name="c"></a>[C#](#tab/csharp)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-c"></a>C# を使用して、フォロワー クラスターにアタッチされたフォロワー データベースをデタッチする


フォロワー クラスターにアタッチされたフォロワー データベースは、次の方法でデタッチできます。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-c"></a>C# を使用して、リーダー クラスターにアタッチされたフォロワー データベースをデタッチする

リーダー クラスターにアタッチされたデータベースは、次の方法でデタッチできます。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

# <a name="python"></a>[Python](#tab/python)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-python"></a>Python を使用して、フォロワー クラスターにアタッチされたフォロワー データベースをデタッチする

フォロワー クラスターにアタッチされたデータベースは、次の方法でデタッチできます。

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-python"></a>Python を使用して、リーダー クラスターにアタッチされたフォロワー データベースをデタッチする

リーダー クラスターにアタッチされたデータベースは、次の方法でデタッチできます。

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="detach-a-database-using-powershell"></a>PowerShell を使用してデータベースをデタッチする

#### <a name="needed-modules"></a>必要なモジュール

```
Install : Az.Kusto
```

#### <a name="example"></a>例

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "sanjn"  ## Can be specific database name or * for all databases

##Construct the Configuration name 
$confignameraw = (Get-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -ResourceGroupName $FollowerResourceGroupName -SubscriptionId $FollowerClusterSubscriptionID) | Where-Object {$_.DatabaseName -eq $DatabaseName }
$configname =$confignameraw.Name.Split("/")[1]

Remove-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -Name $configname -ResourceGroupName $FollowerResourceGroupName
```

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

C#、Python、または PowerShell を使用して[フォロワー データベースをデタッチ](#detach-the-follower-database)します。

---

## <a name="manage-principals-permissions-and-caching-policy"></a>プリンシパル、アクセス許可、キャッシュ ポリシーの管理

### <a name="manage-principals"></a>プリンシパルの管理

データベースをアタッチするときは、 **"既定のプリンシパル変更の種類"** を指定します。 既定では、[承認されたプリンシパル](kusto/management/access-control/index.md#authorization)のリーダー データベース コレクションが保持されます。

|**種類** |**説明**  |
|---------|---------|
|**Union (結合)**     |   アタッチされたデータベース プリンシパルには、元のデータベース プリンシパルと、フォロワー データベースに追加されたその他の新しいプリンシパルが常に含まれます。      |
|**Replace**   |    元のデータベースから継承されるプリンシパルはありません。 アタッチされたデータベース用に新しいプリンシパルを作成する必要があります。     |
|**なし**   |   アタッチされたデータベース プリンシパルには、元のデータベースのプリンシパルのみが含まれます。その他のプリンシパルはありません。      |

管理コマンドを使用して承認されたプリンシパルを構成する方法について詳しくは、「[フォロワー クラスターを管理するための管理コマンド](kusto/management/cluster-follower.md)」をご覧ください。

### <a name="manage-permissions"></a>アクセス許可の管理

読み取り専用データベースのアクセス許可は、他のデータベースの種類と同じ方法で管理できます。 「[Azure portal でアクセス許可を管理する](manage-database-permissions.md#manage-permissions-in-the-azure-portal)」を参照してください。

### <a name="configure-caching-policy"></a>キャッシュ ポリシーの構成

フォロワー データベースの管理者は、ホスティング クラスター上でアタッチされたデータベースまたはその任意のテーブルの[キャッシュ ポリシー](kusto/management/cache-policy.md) を変更できます。 既定では、リーダー データベースのデータベース コレクションとテーブル レベルのキャッシュ ポリシーが保持されます。 たとえば、リーダー データベースでは月次レポートを実行するために 30 日間のキャッシュ ポリシーを設定し、フォロワー データベースでは、トラブルシューティング用に最新のデータのみのクエリを実行するため、3 日間のキャッシュ ポリシーを設定することができます。 管理コマンドを使用してフォロワー データベースまたはテーブルのキャッシュ ポリシーを構成する方法について詳しくは、「[フォロワー クラスターを管理するための管理コマンド](kusto/management/cluster-follower.md)」をご覧ください。

## <a name="notes"></a>Notes

* リーダー/フォロワー クラスターのデータベース間に競合がある場合、フォロワー クラスターがすべてのデータベースをフォローすると、次のように解決されます。
  * フォロワー クラスターで作成された *DB* という名前のデータベースは、リーダー クラスターで作成された同じ名前のデータベースよりも優先されます。 そのため、フォロワー クラスターでリーダーのデータベース *DB* を含めるために、フォロワー クラスターのデータベース *DB* を削除または名前変更する必要があります。
  * 2 つ以上のリーダー クラスターからフォローされる *DB* という名前のデータベースは、リーダー クラスターの "*いずれか*" から任意に選択され、複数回フォローされなくなります。
* [クラスターのアクティビティ ログと履歴](kusto/management/systeminfo.md)を表示するためのコマンドをフォロワー クラスターで実行すると、フォロワー クラスターのアクティビティと履歴が表示され、その結果セットには、1 つ以上のリーダー クラスターのそれらの結果は含まれません。
  * たとえば、`.show queries` コマンドをフォロワー クラスターで実行すると、フォロワー クラスターがフォローするデータベースで実行されたクエリのみが表示され、リーダー クラスター内の同じデータベースに対して実行されたクエリは表示されません。
  
## <a name="limitations"></a>制限事項

* フォロワー クラスターとリーダー クラスターは、同じリージョンに存在する必要があります。
* [ストリーミング インジェスト](ingest-data-streaming.md)は、フォローされているデータベースでは使用できません。
* [カスタマー マネージド キー](security.md#customer-managed-keys-with-azure-key-vault)を使用したデータ暗号化は、リーダーとフォロワーの両方のクラスターでサポートされていません。 
* 別のクラスターにアタッチされているデータベースは、デタッチ前に削除することはできません。
* 別のクラスターにアタッチされているデータベースを持つクラスターは、デタッチ前に削除することはできません。

## <a name="next-steps"></a>次のステップ

* フォロワー クラスター構成の詳細については、「[フォロワー クラスターを管理するための管理コマンド](kusto/management/cluster-follower.md)」をご覧ください。
