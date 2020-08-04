---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: orspodek
ms.openlocfilehash: d55077b5d1938caf6df49d34e68ece7e32c56f85
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350071"
---
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 

### <a name="clean-up-resources-using-the-azure-portal"></a>Azure portal を使用してリソースをクリーンアップする

「[リソースのクリーンアップ](../create-cluster-database-portal.md#clean-up-resources)」の手順に従って、Azure portal でリソースを削除します。

### <a name="clean-up-resources-using-powershell"></a>PowerShell を使用してリソースをクリーンアップする

Cloud Shell がまだ開いている場合は、最初の行 (Read-Host) をコピー/実行する必要はありません。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```