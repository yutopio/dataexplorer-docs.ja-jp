---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: de8a37b1b27dc1bff377c6212c0bfea0a13c7de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492687"
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