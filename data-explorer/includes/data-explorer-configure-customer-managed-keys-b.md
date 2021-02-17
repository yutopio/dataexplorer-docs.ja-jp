---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 6e9c489850d77155ca4832275883c7749edd6284
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "98571765"
---
## <a name="create-a-new-key-vault"></a>新しいキー コンテナーを作成する

PowerShell を使用して、新しいキー コンテナーを作成するには、[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) を呼び出します。 Azure Data Explorer 暗号化用のカスタマー マネージド キーの格納に使用するキー コンテナーには、2 つのキー保護設定 ( **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** ) を有効にする必要があります。 次の例のかっこ内のプレースホルダー値は、独自の値に置き換えてください。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを構成する

次に、キー コンテナーのアクセス ポリシーを構成し、クラスターからアクセスできるようにします。 この手順では、先ほどクラスターに割り当てた、システム割り当てまたはユーザー割り当てのマネージド ID を使用します。 キー コンテナーのアクセス ポリシーを設定するには、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

システム割り当て ID の場合、クラスターの principalId を使用します。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

ユーザー割り当て ID の場合、ID の principalId を使用します。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $userIdentity.Properties.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>新しいキーを作成する

次に、キー コンテナーに新しいキーを作成します。 新しいキーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) を呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
