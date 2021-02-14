---
title: Azure Data Explorer 用の組み込みポリシー定義
description: Azure Data Explorer 用の Azure Policy 組み込みポリシー定義を一覧表示します。 これらの組み込みポリシー定義は、Azure リソースを管理するための一般的な方法を示します。
ms.date: 02/05/2021
ms.topic: reference
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-reference
ms.openlocfilehash: 3c3c680d7c3cb0b35c925f840cd6504d3b288776
ms.sourcegitcommit: d1c2433df183d0cfbfae4d3b869ee7f9cbf00fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99586407"
---
# <a name="azure-policy-built-in-definitions-for-azure-data-explorer"></a>Azure Data Explorer 用の Azure Policy 組み込み定義

このページは、Azure Data Explorer 用の [Azure Policy](/azure/governance/policy/overview) 組み込みポリシー定義のインデックスです。 他のサービス用の Azure Policy 組み込みについては、[Azure Policy 組み込み定義](/azure/governance/policy/samples/built-in-policies)に関するページをご覧ください。

各組み込みポリシー定義の名前は、Azure portal のポリシー定義にリンクしています。 **[バージョン]** 列のリンクを使用すると、[Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のソースを表示できます。

## <a name="azure-data-explorer"></a>Azure Data Explorer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer における保存時の暗号化でカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Azure Data Explorer クラスターでカスタマー マネージド キーを使用した保存時の暗号化を有効にすると、保存時の暗号化で使用されるキーをさらに制御できるようになります。 この機能は、多くの場合、特別なコンプライアンス要件を持つ顧客に適用でき、キーの管理に Key Vault を必要とします。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Azure Data Explorer でディスク暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |ディスク暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer で二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |二重暗号化を有効にすると、データを保護して、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 二重暗号化が有効になっている場合、ストレージ アカウント内のデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 (サービス レベルで 1 回、インフラストラクチャ レベルで 1 回) 暗号化されます。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Azure Data Explorer に対して仮想ネットワークの挿入を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |ネットワーク セキュリティ グループ規則を適用し、オンプレミスで接続し、サービス エンドポイントを使用してデータ接続ソースのセキュリティで保護できるようにする、仮想ネットワークの挿入によってネットワーク境界をセキュリティで保護します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

## <a name="next-steps"></a>次のステップ

- [Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のビルトインを参照します。
- 「[Azure Policy の定義の構造](/azure/governance/policy/concepts/definition-structure)」を確認します。
- 「[Policy の効果について](/azure/governance/policy/concepts/effects)」を確認します。
