---
title: Azure Data Explorer 用の Azure セキュリティ ベースライン
description: Azure Data Explorer セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c6cf97837f0da34a63f3b9274d160650ab2349fb
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554921"
---
# <a name="azure-security-baseline-for-azure-data-explorer"></a>Azure Data Explorer 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](/azure/security/benchmarks/overview-v1) のガイダンスが Azure Data Explorer に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Data Explorer に適用できる関連ガイダンスによって定義されています。 Azure Data Explorer に適用できない **制御** は、除外されています。

 
Azure Data Explorer を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Data Explorer セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Data Explorer では、ご利用の仮想ネットワーク内のサブネットへのクラスターのデプロイがサポートされています。 この機能を使用すれば、ご利用の Azure Data Explorer クラスターのトラフィックにネットワーク セキュリティ グループ (NSG) 規則を適用し、ご利用のオンプレミスのネットワークを Azure Data Explorer クラスターのサブネットに接続し、ご利用のデータ接続ソース (Event Hub や Event Grid) をサービス エンドポイントによってセキュリティで保護することができます。

- [Virtual Network にクラスターを作成する方法](./vnet-create-cluster-portal.md)

- [Azure Data Explorer クラスターを Virtual Network にデプロイする方法](./vnet-deployment.md)

- [Virtual Network クラスターの作成、接続、操作のトラブルシューティング方法](./vnet-deploy-troubleshoot.md?tabs=windows)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

- [NSG フロー ログを有効にする方法](/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](/azure/security-center/security-center-network-recommendations)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:DDoS 攻撃から保護するために、ご利用の Azure Data Explorer クラスターを保護する仮想ネットワーク上で Azure DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](/azure/virtual-network/manage-ddos-protection)

- [Azure Security Center の統合された脅威インテリジェンスについて](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: ご利用の Azure Data Explorer クラスターを保護するために使用されるネットワーク セキュリティ グループ (NSG) に関するフロー ログを有効にし、トラフィックの監査のためにストレージ アカウントにログを送信します。

- [NSG フロー ログを有効にする方法](/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: 仮想ネットワーク サービス タグを使用することで、ご利用の Azure Data Explorer クラスターに関連付けられているネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグの概要と使用方法](/azure/virtual-network/service-tags-overview)

- [Azure Data Explorer におけるサービス タグ構成要件](./vnet-deployment.md#dependencies-for-vnet-deployment)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: 顧客は Azure Policy を使用して、ネットワーク リソース用の標準的なセキュリティ構成を定義し、実装できます。

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure RBAC の制御、Azure Policy の割り当てなどの主要な環境成果物を、単一のブループリント定義にパッケージ化することにより、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

- [Azure Policy を構成して管理する方法](/azure/governance/policy/tutorials/create-and-manage)

- [Azure Blueprint を作成する方法](/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:ご利用の Azure Data Explorer クラスターに対するネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure Policy を使用して、ネットワーク リソースの構成の検証 (または修復、あるいはその両方) を行います。

- [Azure Policy を構成して管理する方法](/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Data Explorer は、診断ログを使用してインジェストの成功と失敗に関する分析情報を取得します。 操作ログを Azure Storage、イベント ハブ、または Log Analytics にエクスポートして、インジェスト ステータスを監視することができます。

- [Azure Data Explorer のインジェスト操作を監視する方法](./using-diagnostic-logs.md)

- [Azure Data Explorer で監視データを取り込んでクエリを実行する方法](./ingest-data-no-code.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 特定の操作とログ記録を使用するには、アクセスとログ記録に関する Azure Data Explorer の診断設定を有効にします。 Azure Monitor 内の Azure のアクティビティ ログ (リソースに関する概要レベルのログが含まれる) は、既定で有効になっています。

- [Azure Data Explorer のインジェスト操作を監視する方法](./using-diagnostic-logs.md)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure プラットフォーム ログの概要](/azure/azure-monitor/platform/platform-logs-overview)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかどうかログを分析および監視し、結果を定期的に確認します。 Azure Data Explorer 用の診断設定を有効にしたら、Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

- [Log Analytics ワークスペースについて](/azure/azure-monitor/log-query/get-started-portal)

- [Azure Monitor でカスタム クエリを実行する方法](/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure Data Explorer では、データベースやテーブルなどのセキュリティで保護されたリソースを操作するためのアクセス許可を持つセキュリティ プリンシパル (ユーザーおよびアプリケーション) と、許可される操作が、セキュリティ ロールによって定義されます。 Kusto クエリを活用すれば、Azure Data Explorer クラスターおよびデータベース用の管理者ロールでプリンシパルを一覧表示できます。

- [Kusto クエリを使用した Azure Data Explorer でのセキュリティ ロールの管理](/azure/kusto/management/security-roles)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションとマーケットプレース サービスについては、お客様が責任を負うものとします。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 顧客は、専用管理者アカウントの使用に関する標準的な操作手順を作成できます。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

顧客はまた、Microsoft サービスの Azure Active Directory (Azure AD) Privileged Identity Management の特権ロール、および Azure ARM を使用して、Just-In-Time または Just-Enough-Access を有効にすることもできます。 

- [Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: 可能な限り、顧客はサービスごとに個別のスタンドアロン資格情報を構成するのではなく、SSO を Azure Active Directory (Azure AD) と一緒に使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

- [Azure AD を使用した SSO について](/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証 (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](/azure/active-directory/authentication/howto-mfa-getstarted)

- [Azure Security Center で ID とアクセスを監視する方法](/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証 (MFA) が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure リソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で MFA を有効にする方法](/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 顧客は、条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国もしくはリージョンの特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) は、Azure Data Explorer に対する認証方法として推奨されています。 Azure AD では、次のようなさまざまな認証シナリオがサポートされています。

- ユーザー認証 (対話型ログオン): 人間のプリンシパルを認証するために使用されます。

- アプリケーション認証 (非対話型ログオン): 人間のユーザーがいない状態で実行または認証する必要があるサービスおよびアプリケーションを認証するために使用されます。

詳細については、次のリファレンスを参照してください。

- [Azure Data Explorer アクセス制御の概要](/azure/kusto/management/access-control)

- [Azure Active Directory での認証](/azure/kusto/management/access-control/aad)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

- [Azure AD により Azure Data Explorer アクセスを認証する方法](/azure/kusto/management/access-control/how-to-authenticate-with-aad)

- [Azure AD レポート](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースを使用して監視を行うことができるため、任意のセキュリティ情報イベント管理 (SIEM) または監視ツールとの統合が可能です。

 このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 顧客は Log Analytics ワークスペースで必要なアラートを構成します。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) リスク検出および Identity Protection 機能を使用します。 また、さらに詳しく調査するためにデータを Azure Sentinel に取り込むこともできます。

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、カスタマー ロックボックスに、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。

- [カスタマー ロックボックスについて](/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure Data Explorer クラスターについては、仮想ネットワークまたはサブネットによって他のリソースから分離し、適切にタグ付けを行い、ネットワーク セキュリティ グループ (NSG) または Azure Firewall 内でセキュリティで保護する必要があります。 機密データを格納または処理する Azure Data Explorer クラスターは、十分に分離する必要があります。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Data Explorer クラスターを仮想ネットワークにデプロイする方法](./vnet-deployment.md)

- [セキュリティ構成を使用して NSG を作成する方法](/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure Data Explorer クラスターでは、既定で TLS 1.2 がネゴシエートされます。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

**Azure Security Center の監視**: 適用外

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Data Explorer では、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure Data Explorer を使用すると、Azure のロールベースのアクセス制御 (RBAC) モデルを使用して、データベースとテーブルへのアクセスを制御することができます。 このモデルでは、プリンシパル (ユーザー、グループ、およびアプリ) がロールにマッピングされます。 プリンシパルは、割り当てられたロールに従ってリソースにアクセスできます。

- [Azure Data Explorer の RBAC を構成するためのロールとアクセス許可のリストおよび手順](./manage-database-permissions.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Disk Encryption は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 クラスター仮想マシンの OS とデータ ディスクのボリューム暗号化が提供されます。 また、Azure Key Vault とも統合されます。これにより、ディスク暗号化キーとシークレットを制御および管理できると共に、Azure Storage で保存中の VM ディスクの全データが確実に暗号化されるようにすることができます。

- [Azure Data Explorer クラスターで保存時の暗号化を有効にする方法](./cluster-disk-encryption.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: ご利用の Azure Data Explorer クラスター上でリソース レベルの変更が行われたときにアラートを作成するには、Azure Monitor を Azure アクティビティ ログと一緒に使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。

- [Azure Security Center のセキュリティ スコアについて](/azure/security-center/security-center-secure-score)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソースのクエリを実行して検出します。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](/azure/governance/resource-graph/first-query-portal)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC について](/azure/role-based-access-control/overview)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 資産の整理と追跡を行うには、適切な名前付け規則、タグ付け、管理グループ、または個別のサブスクリプションを必要に応じて使用できます。 Azure Resource Graph を使用すれば、定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで確実に削除されるようにすることができます。 

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Resource Graph を使用してクエリを作成する方法](/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成する必要があります。

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:Azure Policy を使用すると、次の組み込みのポリシー定義によって、顧客のサブスクリプション内に作成できるリソースの種類に制限を設けることができます。

- 許可されないリソースの種類

- 許可されるリソースの種類

Azure Monitor を使用して監視できるアクティビティ ログを使用して、ポリシーによって生成されたイベントを監視することができます。

さらに、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行うことができます。

- [コンプライアンスを強制するポリシーの作成と管理](/azure/governance/policy/tutorials/create-and-manage)

- [クイック スタート: Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](/azure/governance/resource-graph/first-query-portal)

- [Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用すると、次の組み込みのポリシー定義によって、顧客のサブスクリプション内に作成できるリソースの種類に制限を設けることができます。

 - 許可されないリソースの種類

 - 許可されるリソースの種類

詳細については、次のリファレンスを参照してください。

- [コンプライアンスを強制するポリシーの作成と管理](/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy のサンプル](/azure/governance/policy/samples/built-in-policies#general)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、ご利用の Azure サブスクリプション内でのリソースの作成と変更ができなくなります。 

- [条件付きアクセスを使用して Azure 管理へのアクセスを管理する](/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: ご利用の Azure リソースの構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用することもできます。

また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があります。これを確認して、構成が確実に組織のセキュリティ要件を満たすかそれを超えるようにする必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からのレコメンデーションを使用することもできます。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](/azure/governance/policy/tutorials/create-and-manage)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](/azure/azure-resource-manager/templates/export-template-portal)

- [セキュリティの推奨事項 - リファレンス ガイド](/azure/security-center/recommendations-reference)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。  Change Tracking、ポリシー コンプライアンス ダッシュボード、カスタム ソリューションなどのソリューションを使用すれば、ご利用の環境内でのセキュリティの変更を容易に特定できます。

- [Azure Policy の効果について](/azure/governance/policy/concepts/effects)

- [コンプライアンスを強制するポリシーの作成と管理](/azure/governance/policy/tutorials/create-and-manage)

- [Change Tracking ソリューションを使用して環境内の変更を追跡する](/azure/automation/change-tracking)

- [Azure リソースのコンプライアンス データを取得する](/azure/governance/policy/how-to/get-compliance-data)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなど、ご利用のコードを安全に格納して管理するには、Azure Repos を使用します。Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。  さらに、Azure Automation を使用して、構成の変更をデプロイすることもできます。

- [Azure Policy を構成して管理する方法](/azure/governance/policy/tutorials/create-and-manage)

- [エイリアスを使用する方法](/azure/governance/policy/concepts/definition-structure#aliases)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: システム構成に関するアラートの生成、システム構成の監査および適用を行うには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 ご利用の Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [マネージド ID を構成する方法](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

- [Azure Data Explorer クラスターのマネージド ID を構成する](./managed-identities.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用外

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Data Explorer など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Azure Data Explorer、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

**Azure Security Center の監視**: 適用外

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:ご利用の Azure Data Explorer クラスターで使用される Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を確保するため、常にレプリケートされています。 Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するためにデータがコピーされます。 同じデータ センター内、同じリージョン内の複数のゾーン データ センター間、または地理的に分離されたリージョン間でデータをレプリケートすることもできます。

- [Azure Storage の冗長性とサービス レベル アグリーメントについて](/azure/storage/common/storage-redundancy)

- [データをストレージにエクスポートする](/azure/kusto/management/data-export/export-data-to-storage)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Data Explorer は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、データの暗号化に使用する目的で、カスタマー マネージド キーを提供できます。 顧客が管理するキーは Azure Key Vault に格納する必要があります。

- [Azure portal を使用してカスタマー マネージド キーを構成する](./customer-managed-keys-portal.md)

- [C# を使用してカスタマー マネージド キーを構成する](./customer-managed-keys-csharp.md)

- [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](./customer-managed-keys-resource-manager.md)

- [Azure Key Vault の証明書をバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: ご利用の Azure Key Vault シークレットのデータ復元を定期的にテストします。

- [Azure Key Vault の証明書を復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

- [C# を使用してカスタマー マネージド キーを構成する](./customer-managed-keys-csharp.md)

- [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](./customer-managed-keys-resource-manager.md)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: 顧客は Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護することができます。  また、コンテナーまたはオブジェクトが削除状態にある場合に保持期間が経過するまでそれらを消去できないように、削除保護を有効にすることもできます。  

- [Key Vault で論理的な削除と消去保護を有効にする方法](/azure/key-vault/key-vault-ovw-soft-delete)

- [C# を使用してカスタマー マネージド キーを構成する](./customer-managed-keys-csharp.md)

- [Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する](./customer-managed-keys-resource-manager.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。
    

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

    

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

    

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](/azure/security-center/security-center-alerts-overview)

- [タグを使用した Azure リソースの整理](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。
    

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。
    

- [Azure Security Center のセキュリティ連絡先を設定する方法](/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。
    

- [連続エクスポートを構成する方法](/azure/security-center/continuous-export)

    

- [Azure Sentinel にアラートをストリーミングする方法](/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。
    

- [ワークフローの自動化と Logic Apps を構成する方法](/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用外

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
