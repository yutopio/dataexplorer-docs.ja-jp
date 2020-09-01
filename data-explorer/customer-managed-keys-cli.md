---
title: Azure CLI を使用してカスタマー マネージド キーを構成する
description: この記事では、Azure CLI を使用して Azure Data Explorer のデータに対するカスタマー マネージド キーの暗号化を構成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: astauben
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/01/2020
ms.openlocfilehash: c343ac1177e439f75116a3da77def5862d5d2a7f
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872133"
---
# <a name="configure-customer-managed-keys-using-azure-cli"></a>Azure CLI を使用してカスタマー マネージド キーを構成する

> [!div class="op_single_selector"]
> * [ポータル](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager テンプレート](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-azure-cli"></a>Azure CLI を使用してカスタマー マネージド キーによる暗号化を有効にする
この記事では、Azure CLI クライアントを使用してカスタマー マネージド キーによる暗号化を有効にする方法について説明します。 既定では、Azure Data Explorer の暗号化では Microsoft のマネージド キーが使用されます。 カスタマー マネージド キーを使用するように Azure Data Explorer クラスターを構成し、そのクラスターに関連付けるキーを指定します。

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurecli-interactive
    az login
    ```

1. クラスターが登録されているサブスクリプションを設定します。 *MyAzureSub* は、使用する Azure サブスクリプションの名前に置き換えてください。

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

1. 次のコマンドを実行して新しいキーを設定します。
    ```azurecli-interactive
    az kusto cluster update --cluster-name "mytestcluster" --resource-group "mytestrg" --key-vault-properties key-name="<key-name>" key-version="<key-version>" key-vault-uri="<key-vault-uri>"
    ```
1. 次のコマンドを実行し、'keyVaultProperties' プロパティをチェックして、クラスターが正常に更新されたことを確認します。

    ```azurecli-interactive
    az kusto cluster show --cluster-name "mytestcluster" --resource-group "mytestrg"
    ```

