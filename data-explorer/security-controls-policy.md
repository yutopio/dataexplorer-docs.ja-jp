---
title: Azure Data Explorer 用の Azure Policy 規制コンプライアンス コントロール
description: Azure Data Explorer に対して使用できる Azure Policy 規制コンプライアンス コントロールの一覧を示します。 これらの組み込みポリシー定義により、Azure リソースのコンプライアンスを管理するための一般的な方法が提供されます。
ms.date: 02/05/2021
ms.topic: sample
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 9f52935e235dcf64c5236d3fbdd58b95441aa5c9
ms.sourcegitcommit: d1c2433df183d0cfbfae4d3b869ee7f9cbf00fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99586390"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-data-explorer"></a>Azure Data Explorer 用の Azure Policy 規制コンプライアンス コントロール

[Azure Policy の規制コンプライアンス](/azure/governance/policy/concepts/regulatory-compliance)により、さまざまなコンプライアンス基準に関連する **コンプライアンス ドメイン** および **セキュリティ コントロール** に対して、"_組み込み_" と呼ばれる、Microsoft が作成および管理するイニシアチブ定義が提供されます。 このページでは、Azure Data Explorer 用の **コンプライアンス ドメイン** と **セキュリティ コントロール** の一覧を示します。 **セキュリティ コントロール** の組み込みを個別に割り当てることで、Azure リソースを特定の基準に準拠させることができます。

各組み込みポリシー定義のタイトルは、Azure portal のポリシー定義にリンクしています。 **[ポリシーのバージョン]** 列のリンクを使用すると、[Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のソースを表示できます。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](/azure/governance/policy/overview) 定義に関連します。 これらのポリシーは、コントロールに対する[コンプライアンスの評価](/azure/governance/policy/how-to/get-compliance-data)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** は、ポリシー自体のみを指しています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 これらのコンプライアンス標準に対するコントロールと Azure Policy 規制コンプライアンス定義の間の関連付けは、時間の経過と共に変わる可能性があります。

## <a name="cmmc-level-3"></a>CMMC レベル 3

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - CMMC レベル 3](/azure/governance/policy/samples/cmmc-l3) に関する記事をご覧ください。 このコンプライアンス標準の詳細については、[サイバーセキュリティ成熟度モデル認定 (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf) に関するページをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |SC.3.177 |CUI の機密性を保護するために使用する場合は、FIPS 検証済みの暗号化を採用する。 |[Azure Data Explorer における保存時の暗号化でカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|システムと通信の保護 |SC.3.177 |CUI の機密性を保護するために使用する場合は、FIPS 検証済みの暗号化を採用する。 |[Azure Data Explorer でディスク暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|システムと通信の保護 |SC.3.177 |CUI の機密性を保護するために使用する場合は、FIPS 検証済みの暗号化を採用する。 |[Azure Data Explorer で二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|システムと通信の保護 |SC.3.191 |保存時の CUI の機密性を保護する。 |[Azure Data Explorer でディスク暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|システムと通信の保護 |SC.3.191 |保存時の CUI の機密性を保護する。 |[Azure Data Explorer で二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |

## <a name="next-steps"></a>次のステップ

- [Azure Policy の規制コンプライアンス](/azure/governance/policy/concepts/regulatory-compliance)の詳細を確認します。
- [Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のビルトインを参照します。
