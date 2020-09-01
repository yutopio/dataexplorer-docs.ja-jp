---
title: Azure Resource Manager テンプレートを使用して Azure Data Explorer 用のデータベース プリンシパルを追加する
description: この記事では、Azure Resource Manager テンプレートを使用して Azure Data Explorer 用のデータベース プリンシパルを追加する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 02/03/2020
ms.openlocfilehash: b48bae5301f7a9a0387b110f1f69888834448ad2
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873051"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure Data Explorer 用のデータベース プリンシパルを追加する

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager テンプレート](database-principal-resource-manager.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 この記事では、Azure Resource Manager テンプレートを使用して Azure Data Explorer 用のデータベース プリンシパルを追加します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [クラスターとデータベース](create-cluster-database-portal.md)を作成します

## <a name="azure-resource-manager-template-for-adding-a-database-principal"></a>データベース プリンシパルを追加するための Azure Resource Manager テンプレート

次の例は、データベース プリンシパルを追加するための Azure Resource Manager テンプレートを示しています。  フォームを使用して、[Azure portal でテンプレートの編集とデプロイを実行](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template)できます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "mykustodatabase",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "principalIdForDatabase": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForDatabasePrincipal": {
            "type": "string",
            "defaultValue": "Admin",
            "metadata": {
                "description": "Specifies the database principal role. It can be 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'"
            }
        },
        "tenantIdForDatabasePrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the database principal"
            }
        },
        "principalTypeForDatabase": {
            "type": "string",
            "defaultValue": "App",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('databaseName'), '/', parameters('principalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForDatabase')]",
                "role": "[parameters('roleForDatabasePrincipal')]",
                "tenantId": "[parameters('tenantIdForDatabasePrincipal')]",
                "principalType": "[parameters('principalTypeForDatabase')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

* [イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md)
