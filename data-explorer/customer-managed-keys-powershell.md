---
title: PowerShell を使用してカスタマー マネージド キーを構成する
description: この記事では、PowerShell を使用して Azure Data Explorer のデータに対するカスタマー マネージド キーの暗号化を構成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: roshauli
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/04/2020
ms.openlocfilehash: 5c52253a6d8c4978931aeff3a5a5a73367f14ce5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871997"
---
# <a name="configure-customer-managed-keys-using-powershell"></a>PowerShell を使用してカスタマー マネージド キーを構成する

> [!div class="op_single_selector"]
> * [ポータル](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager テンプレート](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-powershell"></a>PowerShell を使用してカスタマー マネージド キーによる暗号化を有効にする

この記事では、PowerShell を使用してカスタマー マネージド キーによる暗号化を有効にする方法について説明します。 既定では、Azure Data Explorer の暗号化では Microsoft のマネージド キーが使用されます。 カスタマー マネージド キーを使用するように Azure Data Explorer クラスターを構成し、そのクラスターに関連付けるキーを指定します。

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. クラスターが登録されているサブスクリプションを設定します。

    ```azurepowershell-interactive
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

1. 次のコマンドを実行して新しいキーを設定します。

    ```azurepowershell-interactive
    Update-AzKustoCluster -ResourceGroupName "mytestrg" -Name "mytestcluster" -KeyVaultPropertyKeyName "<key-name>" -KeyVaultPropertyKeyVaultUri "<key-vault-uri>" -KeyVaultPropertyKeyVersion "<key-version>"
    ```

1. 次のコマンドを実行し、'KeyVaultProperty...' プロパティをチェックして、クラスターが正常に更新されたことを確認します。

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name "mytestcluster" -ResourceGroupName "mytestrg" | Format-List
    ```
