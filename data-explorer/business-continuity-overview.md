---
title: Azure Data Explorer および事業継続とディザスター リカバリー
description: この記事では、破壊的イベントから復旧するための Azure Data Explorer の機能について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 2dcb1fb83d592ff7fd81fcfc5f51cf6fc95254b7
ms.sourcegitcommit: a36981785765b85a961f275be24d297d38e498fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310036"
---
# <a name="business-continuity-and-disaster-recovery-overview"></a>事業継続とディザスター リカバリーの概要

Azure Data Explorer における事業継続とディザスター リカバリーにより、中断が発生しても業務を継続できます。 この記事では、可用性 (リージョン内) とディザスター リカバリーについて説明します。 回復性がある Azure Data Explorer のデプロイのためのネイティブな機能とアーキテクチャに関する考慮事項について説明します。 人的エラーからの復旧、高可用性、およびディザスター リカバリーの複数の構成について詳しく説明します。 これらの構成は、目標復旧時点 (RPO) と目標復旧時間 (RTO)、必要な労力、コストなど、回復性の要件によって異なります。

## <a name="mitigate-disruptive-events"></a>破壊的イベントを軽減する

* [人的エラー](#human-error)
* [Azure Data Explorer の高可用性](#high-availability-of-azure-data-explorer)
* [Azure 可用性ゾーンの停止](#outage-of-an-azure-availability-zone)
* [Azure データセンターの停止](#outage-of-an-azure-datacenter)
* [Azure リージョンの停止](#outage-of-an-azure-region)

### <a name="human-error"></a>人的エラー 

人的エラーを避けることはできません。 ユーザーは、クラスター、データベース、またはテーブルを誤って削除することがあります。  

#### <a name="accidental-cluster-or-database-deletion"></a>クラスターまたはデータベースの誤った削除

クラスターまたはデータベースの誤った削除は復旧不可能な操作です。 Azure Data Explorer リソースの所有者は、Azure リソース レベルで利用可能な削除[ロック](/azure/azure-resource-manager/management/lock-resources)機能を有効にすることで、データの損失を防ぐことができます。

#### <a name="accidental-table-deletion"></a>テーブルの誤った削除

テーブル管理者以上のアクセス許可を持つユーザーは、[テーブルを削除する](kusto/management/drop-table-command.md)ことができます。 そのようなユーザーの 1 人が誤ってテーブルを削除した場合は、[`.undo drop table`](kusto/management/undo-drop-table-command.md) コマンドを使用してそのテーブルを復旧できます。 このコマンドを正常に実行するには、最初に [アイテム保持ポリシー](kusto/management/retentionpolicy.md)の "*回復性*" プロパティを有効にする必要があります。

#### <a name="accidental-external-table-deletion"></a>外部テーブルの誤った削除

[外部テーブル](kusto/query/schema-entities/externaltables.md)は、データベースの外部に格納されているデータを参照する Kusto スキーマ エンティティです。 外部テーブルを削除すると、テーブルのメタデータのみが削除されます。 テーブル作成コマンドを再実行することにより、それを復旧できます。 ユーザーが構成した時間だけ、ファイルまたは BLOB の誤った削除または上書きを防ぐには、[論理的な削除](/azure/storage/blobs/storage-blob-soft-delete)機能を使用します。

### <a name="high-availability-of-azure-data-explorer"></a>Azure Data Explorer の高可用性

高可用性とは、Azure Data Explorer、そのコンポーネント、および Azure リージョン内の基になる依存関係のフォールト トレランスのことです。 このフォールト トレランスにより、実装における単一障害点 (SPOF) が回避されます。 Azure Data Explorer の高可用性には、永続レイヤー、コンピューティング レイヤー、リーダー フォロワー構成が含まれます。

#### <a name="persistence-layer"></a>永続レイヤー

Azure Data Explorer では、Azure Storage が持続的な永続レイヤーとして利用されます。 Azure Storage では、データ センター内でのローカル冗長ストレージ (LRS) を実現する既定の設定により、フォールト トレランスが自動的に提供されます。 3 つのレプリカが保持されます。 使用中に 1 つのレプリカが失われた場合は、中断なく別のものがデプロイされます。 追加コストはかかりますが、最大限のフォールト トレランスのために Azure リージョンの可用性ゾーンにレプリカがインテリジェントに配置されるゾーン冗長ストレージを使用することで、回復性がさらに向上します。

#### <a name="compute-layer"></a>コンピューティング レイヤー

Azure Data Explorer は分散コンピューティング プラットフォームであり、スケールとノード ロールの種類に応じて、2 つ以上のノードを持つことができます。 プロビジョニング時に、複数の可用性ゾーンを選択し、リージョン内の回復性が最大になるようにゾーン間にノードのデプロイを分散させます。 可用性ゾーンで障害が発生すると、完全に停止するのではなく、ゾーンが復旧するまでパフォーマンスが低下します。 

#### <a name="leader-follower-cluster-configuration"></a>リーダー フォロワー クラスター構成

Azure Data Explorer には、リーダーのデータとメタデータに対する読み取り専用アクセスのために、リーダー クラスターが他のフォロワー クラスターによってフォローされる、オプションの[フォロワー機能](follower.md)が用意されています。 リーダーでの変更 (`create`、`append`、`drop` など) は、フォロワーに自動的に同期されます。 リーダーは複数の Azure リージョンにまたがることができますが、フォロワー クラスターはリーダーと同じリージョンでホストされている必要があります。 リーダー クラスターがダウンした場合、またはデータベースやテーブルが誤って削除された場合は、リーダーでアクセスが復旧されるまで、フォロワー クラスターはアクセスできなくなります。 

### <a name="outage-of-an-azure-availability-zone"></a>Azure 可用性ゾーンの停止

Azure 可用性ゾーンは、同じ Azure リージョン内の一意の物理的な場所です。 部分的なリージョンの障害から Azure Data Explorer クラスターのコンピューティングとデータを保護できます。 ゾーンの障害は、リージョン内であるため可用性のシナリオです。 

Azure Data Explorer クラスターを、接続されている他の Azure リソースと同じゾーンにピン留めします。 可用性ゾーンの有効化の詳細については、「[クラスターの作成](create-cluster-database-portal.md#create-a-cluster)」を参照してください。

> [!NOTE] 
> 可用性ゾーンの選択は、クラスターの作成時にのみサポートされ、後で変更することはできません。

### <a name="outage-of-an-azure-datacenter"></a>Azure データセンターの停止

Azure 可用性ゾーンにはコストがかかります。また、一部のお客様は、ゾーン冗長を使用せずにデプロイすることを選択します。 そのような Azure Data Explorer のデプロイでは、Azure データセンターが停止するとクラスターが停止します。 そのため、Azure データセンターの停止を処理することは、Azure リージョンの停止を処理することと同じです。

### <a name="outage-of-an-azure-region"></a>Azure リージョンの停止

Azure Data Explorer では、Azure リージョン全体の停止に対する自動保護は提供されていません。 そのような障害が発生した場合の事業への影響を最小限に抑えるため、複数の Azure Data Explorer クラスターが [Azure のペアになっているリージョン](/azure/best-practices-availability-paired-regions)にまたがっています。 目標復旧時間 (RTO)、目標復旧時点 (RPO) (RPO)、労力とコストに関する考慮事項に基づいて、[複数のディザスター リカバリー構成](#disaster-recovery-configurations)があります。 コストとパフォーマンスの最適化は、Azure Advisor の推奨事項と[自動スケーリング](manage-cluster-horizontal-scaling.md)の構成で実現できます。

## <a name="disaster-recovery-configurations"></a>ディザスター リカバリーの構成

 このセクションでは、回復性の要件 (RPO と RTO)、必要な作業、コストに応じた、複数のディザスター リカバリー構成について詳しく説明します。

目標復旧時間 (RTO) とは、中断から復旧するまでの時間を指します。 たとえば、RTO が 2 時間の場合は、中断から 2 時間以内にアプリケーションを稼働させる必要があることを意味します。 目標復旧時点 (RPO) とは、その間に失われるデータ量が許容されるしきい値以下である中断時間の長さのことです。 たとえば、RPO が 24 時間で、アプリケーションに 15 年前からのデータが含まれている場合は、合意された RPO のパラメーター内にまだ収まっています。

ディザスター リカバリーを計画するときは、インジェスト、処理、キュレーションを事前に入念に設計する必要があります。 インジェストとは、さまざまなソースから Azure Data Explorer に統合されたデータを指します。処理とは、変換および同様のアクティビティを指します。キュレーションとは、具体化されたビュー、データ レイクへのエクスポートなどのことです。

次に示すのは一般的なディザスター リカバリー構成であり、それぞれについて以下で詳しく説明します。
* [アクティブ/アクティブ/アクティブ (Always On) 構成](#active-active-active-configuration)
* [アクティブ/アクティブ構成](#active-active-configuration)
* [アクティブ/ホット スタンバイ構成](#active-hot-standby-configuration)
* [オンデマンド データ復旧クラスター構成](#on-demand-data-recovery-configuration)

### <a name="active-active-active-configuration"></a>アクティブ/アクティブ/アクティブ構成

この構成は "Always On" とも呼ばれます。 停止が許容されない重要なアプリケーションのデプロイでは、Azure のペアになったリージョンの間で複数の Azure Data Explorer クラスターを使用する必要があります。 すべてのクラスターに対して並列に、インジェスト、処理、キュレーションを設定します。 クラスター SKU は、リージョン間で同じである必要があります。 Azure により、Azure のペアになったリージョン間で更新プログラムが確実にロールアウトされ、時間をずらして調整されます。 1 つの Azure リージョンが停止しても、アプリケーションが停止することはありません。 若干の遅延またはパフォーマンスの低下が発生する可能性があります。

:::image type="content" source="media/business-continuity-overview/active-active-active-n.png" alt-text="アクティブ/アクティブ/アクティブ/n 構成":::

| **構成** | **RPO** | **RTO** | **作業** | **コスト** |
| --- | --- | --- | --- | --- |
| **アクティブ/アクティブ/アクティブ/n** | 0 時間 | 0 時間 | 低 | 最高 |

### <a name="active-active-configuration"></a>アクティブ/アクティブ構成

この構成は[アクティブ/アクティブ/アクティブ構成](#active-active-active-configuration)と同じですが、Azure のペアになった 2 つのリージョンだけが関係します。 デュアル インジェスト、処理、およびキュレーションを構成します。 ユーザーは最も近いリージョンにルーティングされます。 クラスター SKU は、リージョン間で同じである必要があります。

:::image type="content" source="media/business-continuity-overview/active-active.png" alt-text="アクティブ/アクティブ構成":::

| **構成** | **RPO** | **RTO** | **作業** | **コスト** |
| --- | --- | --- | --- | --- |
| **アクティブ/アクティブ** | なし | なし | 低 | 高 |

### <a name="active-hot-standby-configuration"></a>アクティブ/ホット スタンバイ構成

アクティブ/ホット構成は、デュアル インジェスト、処理、キュレーションの[アクティブ/アクティブ構成](#active-active-configuration)に似ています。 ただし、スタンバイ クラスターはエンド ユーザーに対してオフラインであり、プライマリと同じ SKU に存在する必要はありません。 ホット スタンバイ クラスターはさらに小さい SKU とスケールでもよく、そのためパフォーマンスが低下します。 障害のシナリオでは、スタンバイ クラスターがオンラインになり、スケールアップされます。

:::image type="content" source="media/business-continuity-overview/active-hot-standby.png" alt-text="アクティブ/ホット スタンバイ構成":::

| **構成** | **RPO** | **RTO** | **作業** | **コスト** |
| --- | --- | --- | --- | --- |
| **アクティブ/ホット スタンバイ** | 低 | 低 | Medium | Medium |

### <a name="on-demand-data-recovery-configuration"></a>オンデマンド データ復旧構成

このソリューションでは、最低限の回復性 (最も高い RPO と RTO) が提供され、コストが最も低く、労力が最も高くなります。 この構成では、データ復旧クラスターはありません。 GRS (geo 冗長ストレージ) が構成されているストレージ アカウントへのキュレートされたデータの連続エクスポートを構成します (生データと中間データも必要な場合を除く)。 ディザスター リカバリー シナリオがある場合は、データ復旧クラスターがスピンアップされます。 その時点で、DDL、構成、ポリシー、プロセスが適用されます。 データはインジェスト プロパティ [kustoCreationTime](ingest-data-event-grid-overview.md) を使用してストレージから取り込まれ、既定でシステム時刻に設定されているインジェスト時間がオーバーライドされます。 

:::image type="content" source="media/business-continuity-overview/on-demand-data-recovery-cluster.png" alt-text="オンデマンド データ復旧クラスター構成":::

| **構成** | **RPO** | **RTO** | **作業** | **コスト** |
| --- | --- | --- | --- | --- |
| **オンデマンド データ復旧クラスター** | 最高 | 最高 | 最高 | 最低 |

### <a name="summary-of-disaster-recovery-configuration-options"></a>ディザスター リカバリー構成オプションの概要

| **構成** | **回復性** | **RPO** | **RTO** | **作業** | **コスト** |
| --- | --- | --- | --- | --- | --- |
| **アクティブ/アクティブ/アクティブ/n** | 最高 | 0 時間 | 0 時間 | 低 | 最高 |
| **アクティブ/アクティブ** | 高 | なし | なし | 低 | 高 |
| **アクティブ/ホット スタンバイ** | 中間 | 低 | 低 | Medium | Medium |
| **オンデマンド データ復旧クラスター** | 最低 | 最高 | 最高 | 最高 | 最低 |

## <a name="best-practices"></a>ベスト プラクティス

選択されたディザスター リカバリー構成にかかわらず、次のベスト プラクティスに従ってください。

* すべてのデータベース オブジェクト、ポリシー、構成をソース管理に保持して、リリース自動化ツールからクラスターにリリースできるようにする必要があります。 詳細については、[Azure Data Explorer Flow に対する Azure DevOps のサポート](devops.md)に関する記事を参照してください。 
* 検証ルーチンを設計、開発、実装し、すべてのクラスターがデータの観点から確実に同期されるようにします。 Azure Data Explorer では、[クロス クラスター結合](kusto/query/cross-cluster-or-database-queries.md?pivots=azuredataexplorer)がサポートされています。 テーブル全体の単純なカウントまたは行は、検証に役立ちます。
* [連続エクスポート](kusto/management/data-export/continuous-data-export.md)機能を使用し、Azure Data Explorer テーブル内のデータを Azure Data Lake ストアにエクスポートします。 回復力が最も高くなるように GRS を選択します。
* リリース手順には、クラスターのミラーリングを保証するガバナンスのチェックとバランスを含める必要があります。
* 最初からクラスターを構築するために必要なものを完全に理解します。
* デプロイ ユニットのチェックリストを作成します。 リストはニーズに固有ですが、デプロイ スクリプト、インジェスト接続、BI ツール、その他の重要な構成が含まれている必要があります。 

## <a name="next-steps"></a>次のステップ

詳細については、「[Azure Data Explorer を使用して事業継続とディザスター リカバリー ソリューションを作成する](business-continuity-create-solution.md)」を参照してください。
