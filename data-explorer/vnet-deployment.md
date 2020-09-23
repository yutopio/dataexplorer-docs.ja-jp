---
title: Azure Data Explorer を仮想ネットワークにデプロイする
description: Azure Data Explorer を仮想ネットワークにデプロイする方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/31/2019
ms.openlocfilehash: 74d72ced89b1953b2f7e327656517f1febe4166f
ms.sourcegitcommit: 803a572ab6f04494f65dbc60a4c5df7fcebe1600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90714025"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Azure Data Explorer クラスターを仮想ネットワークにデプロイする

この記事では、Azure Data Explorer クラスターをカスタム Azure Virtual Network にデプロイするときに存在するリソースについて説明します。 この情報は、Virtual Network (VNet) のサブネットにクラスターをデプロイする際に役立ちます。 Azure Virtual Network の詳細については、「[Azure Virtual Network とは](/azure/virtual-network/virtual-networks-overview)」をご覧ください。

:::image type="content" source="media/vnet-deployment/vnet-diagram.png" alt-text="仮想ネットワーク アーキテクチャの概略図"::: 

Azure Data Explorer では、Virtual Network (VNet) のサブネットへのクラスターのデプロイがサポートされています。 この機能により、次のことが可能になります。

* Azure Data Explorer クラスター トラフィックに[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview) (NSG) 規則を適用する。
* オンプレミス ネットワークを Azure Data Explorer クラスターのサブネットに接続する。
* [サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)を使用してデータ接続ソース ([Event Hub](/azure/event-hubs/event-hubs-about) と [Event Grid](/azure/event-grid/overview)) のセキュリティを保護する。

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>VNet 内の Azure Data Explorer クラスターにアクセスする

各サービス (エンジンとデータ管理サービス) に対して次の IP アドレスを使用して、Azure Data Explorer クラスターにアクセスできます。

* **プライベート IP**:VNet 内のクラスターにアクセスするために使用されます。
* **[パブリック IP]** : 管理と監視のために VNet の外部からクラスターにアクセスするため、およびクラスターから開始された送信接続の発信元アドレスとして使用されます。

サービスにアクセスするために、次の DNS レコードが作成されます。 

* `[clustername].[geo-region].kusto.windows.net` (エンジン) `ingest-[clustername].[geo-region].kusto.windows.net` (データ管理) は、各サービスのパブリック IP にマップされます。 

* `private-[clustername].[geo-region].kusto.windows.net` (エンジン) `private-ingest-[clustername].[geo-region].kusto.windows.net` (データ管理) は、各サービスのプライベート IP にマップされます。

## <a name="plan-subnet-size-in-your-vnet"></a>VNet でサブネットのサイズを計画する

Azure Data Explorer クラスターをホストするために使用されるサブネットのサイズは、サブネットをデプロイした後に変更することはできません。 VNet では、Azure Data Explorer は VM ごとに 1 つのプライベート IP アドレスを使用し、内部ロード バランサー (エンジンとデータ管理) に 2 つのプライベート IP アドレスを使用します。 さらに Azure ネットワークでは、サブネットごとに 5 つの IP アドレスを使用します。 Azure Data Explorer は、データ管理サービス用に 2 つの VM をプロビジョニングします。 エンジン サービス VM は、ユーザー構成のスケール容量ごとにプロビジョニングされます。

IP アドレスの合計数は次のようになります。

| 用途 | アドレスの数 |
| --- | --- |
| エンジン サービス | インスタンスごとに 1 |
| データ管理サービス | 2 |
| 内部ロード バランサー | 2 |
| Azure 予約アドレス | 5 |
| **合計** | **#engine_instances + 9** |

> [!IMPORTANT]
> サブネットのサイズは、Azure Data Explorer をデプロイした後に変更することはできないため、事前に計画する必要があります。 そのため、必要に応じてサブネット サイズを確保してください。

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Azure Data Explorer に接続するためのサービス エンドポイント

[Azure サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)を使用すると、仮想ネットワークに対して Azure マルチテナント リソースのセキュリティを保護することができます。
Azure Data Explorer クラスターをサブネットにデプロイすると、Azure Data Explorer サブネットの基になるリソースを制限しながら、[Event Hub](/azure/event-hubs/event-hubs-about) または [Event Grid](/azure/event-grid/overview) を使用してデータ接続を設定できます。

> [!NOTE]
> [Storage](/azure/storage/common/storage-introduction) と [[イベント ハブ]](/azure/event-hubs/event-hubs-about) で EventGrid セットアップを使用する場合、サブスクリプションで使用されているストレージ アカウントは、信頼できる Azure プラットフォーム サービスを[ファイアウォール構成](/azure/storage/common/storage-network-security)で許可しながら、Azure Data Explorer のサブネットへのサービス エンドポイントを使用してロックすることができます。しかし、イベント ハブでは、信頼できる [Azure プラットフォーム サービス](/azure/event-hubs/event-hubs-service-endpoints)がサポートされないため、サービス エンドポイントを有効にできません。

## <a name="private-endpoints"></a>プライベート エンドポイント

[プライベート エンドポイント](/azure/private-link/private-endpoint-overview)を使用すると、Azure リソース (Storage/Event Hub/Data Lake Gen 2 など) にプライベートにアクセスし、Virtual Network からのプライベート IP を使用して、リソースを効果的に VNet に取り込むことができます。
VNet から、データ接続によって使用されるリソース (Event Hub や Storage など) および外部テーブル (Storage、Data Lake Gen 2、SQL Database など) への[プライベート エンドポイント](/azure/private-link/private-endpoint-overview)を作成して、基になるリソースにプライベートにアクセスします。

 > [!NOTE]
 > プライベート エンドポイントを設定するには、[DNS の構成](/azure/private-link/private-endpoint-dns)が必要です。[Azure プライベート DNS ゾーン](/azure/dns/private-dns-privatednszone)の設定のみがサポートされています。 カスタムの DNS サーバーはサポートされていません。 

## <a name="dependencies-for-vnet-deployment"></a>VNet デプロイの依存関係

### <a name="network-security-groups-configuration"></a>ネットワーク セキュリティ グループ構成

[ネットワーク セキュリティ グループ (NSG)](/azure/virtual-network/security-overview) は、VNet 内でネットワーク アクセスを制御する機能を提供します。 Azure Data Explorer には、2 つのエンドポイント、HTTPs (443) および TDS (1433) を使用してアクセスできます。 クラスターの管理、監視、および適切な操作のために、これらのエンドポイントへのアクセスを許可するように次の NSG ルールを構成する必要があります。 追加のルールは、セキュリティのガイドラインによって異なります。

#### <a name="inbound-nsg-configuration"></a>受信 NSG 構成

| **用途**   | **From**   | **To**   | **プロトコル**   |
| --- | --- | --- | --- |
| 管理  |[ADX 管理アドレス](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | ADX サブネット: 443  | TCP  |
| 正常性の監視  | [ADX 正常性の監視アドレス](#health-monitoring-addresses)  | ADX サブネット: 443  | TCP  |
| ADX 内部通信  | ADX サブネット:すべてのポート  | ADX サブネット: すべてのポート  | All  |
| Azure Load Balancer の着信を許可 (正常性プローブ)  | AzureLoadBalancer  | ADX サブネット: 80、443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>送信 NSG 構成

| **用途**   | **From**   | **To**   | **プロトコル**   |
| --- | --- | --- | --- |
| Azure Storage への依存関係  | ADX サブネット  | Storage:443  | TCP  |
| Azure Data Lake への依存関係  | ADX サブネット  | AzureDataLake:443  | TCP  |
| EventHub インジェストとサービス監視  | ADX サブネット  | EventHub:443,5671  | TCP  |
| メトリックの発行  | ADX サブネット  | AzureMonitor:443 | TCP  |
| Active Directory (該当する場合) | ADX サブネット | AzureActiveDirectory:443 | TCP |
| 証明機関 | ADX サブネット | Internet:80 | TCP |
| 内部通信  | ADX サブネット  | ADX サブネット: すべてのポート  | All  |
| `sql\_request` と `http\_request` のプラグインに使用されるポート  | ADX サブネット  | Internet:Custom  | TCP  |

### <a name="relevant-ip-addresses"></a>関連する IP アドレス

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure Data Explorer 管理 IP アドレス

> [!NOTE]
> 将来のデプロイでは、AzureDataExplorer サービス タグを使用してください

| リージョン | アドレス |
| --- | --- |
| オーストラリア中部 | 20.37.26.134 |
| オーストラリア中部 2 | 20.39.99.177 |
| オーストラリア東部 | 40.82.217.84 |
| オーストラリア南東部 | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| カナダ中部 | 40.82.188.208 |
| カナダ東部 | 40.80.255.12 |
| インド中部 | 40.81.249.251, 104.211.98.159 |
| 米国中部 | 40.67.188.68 |
| 米国中部 EUAP | 40.89.56.69 |
| 中国東部 2 | 139.217.184.92 |
| 中国北部 2 | 139.217.60.6 |
| 東アジア | 20.189.74.103 |
| 米国東部 | 52.224.146.56 |
| 米国東部 2 | 52.232.230.201 |
| 米国東部 2 EUAP | 52.253.226.110 |
| フランス中部 | 40.66.57.91 |
| フランス南部 | 40.82.236.24 |
| 東日本 | 20.43.89.90 |
| 西日本 | 40.81.184.86 |
| 韓国中部 | 40.82.156.149 |
| 韓国南部 | 40.80.234.9 |
| 米国中北部 | 40.81.45.254 |
| 北ヨーロッパ | 52.142.91.221 |
| 南アフリカ北部 | 102.133.129.138 |
| 南アフリカ西部 | 102.133.0.97 |
| 米国中南部 | 20.45.3.60 |
| 東南アジア | 40.119.203.252 |
| インド南部 | 40.81.72.110, 104.211.224.189 |
| 英国南部 | 40.81.154.254 |
| 英国西部 | 40.81.122.39 |
| USDoD 中部 | 52.182.33.66 |
| USDoD 東部 | 52.181.33.69 |
| USGov アリゾナ | 52.244.33.193 |
| USGov テキサス | 52.243.157.34 |
| USGov バージニア州 | 52.227.228.88 |
| 米国中西部 | 52.159.55.120 |
| 西ヨーロッパ | 51.145.176.215 |
| インド西部 | 40.81.88.112, 104.211.160.120 |
| 米国西部 | 13.64.38.225 |
| 米国西部 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>正常性の監視アドレス

| リージョン | アドレス |
| --- | --- |
| オーストラリア中部 | 191.239.64.128 |
| オーストラリア中部 2 | 191.239.64.128 |
| オーストラリア東部 | 191.239.64.128 |
| オーストラリア南東部 | 191.239.160.47 |
| ブラジル南部 | 23.98.145.105 |
| カナダ中部 | 168.61.212.201 |
| カナダ東部 | 168.61.212.201 |
| インド中部 | 23.99.5.162 |
| 米国中部 | 168.61.212.201、23.101.115.123 |
| 米国中部 EUAP | 168.61.212.201、23.101.115.123 |
| 中国東部 2 | 40.73.96.39 |
| 中国北部 2 | 40.73.33.105 |
| 東アジア | 168.63.212.33 |
| 米国東部 | 137.116.81.189、52.249.253.174 |
| 米国東部 2 | 137.116.81.189、104.46.110.170 |
| 米国東部 2 EUAP | 137.116.81.189、104.46.110.170 |
| フランス中部 | 23.97.212.5 |
| フランス南部 | 23.97.212.5 |
| 東日本 | 138.91.19.129 |
| 西日本 | 138.91.19.129 |
| 韓国中部 | 138.91.19.129 |
| 韓国南部 | 138.91.19.129 |
| 米国中北部 | 23.96.212.108 |
| 北ヨーロッパ | 191.235.212.69、40.127.194.147 |
| 南アフリカ北部 | 104.211.224.189 |
| 南アフリカ西部 | 104.211.224.189 |
| 米国中南部 | 23.98.145.105、104.215.116.88 |
| インド南部 | 23.99.5.162 |
| 東南アジア | 168.63.173.234 |
| 英国南部 | 23.97.212.5 |
| 英国西部 | 23.97.212.5 |
| USDoD 中部 | 52.238.116.34 |
| USDoD 東部 | 52.238.116.34 |
| USGov アリゾナ | 52.244.48.35 |
| USGov テキサス | 52.238.116.34 |
| USGov バージニア州 | 23.97.0.26 |
| 米国中西部 | 168.61.212.201 |
| 西ヨーロッパ | 23.97.212.5、213.199.136.176 |
| インド西部 | 23.99.5.162 |
| 米国西部 | 23.99.5.162、13.88.13.50 |
| 米国西部 2 | 23.99.5.162、104.210.32.14、52.183.35.124 |

## <a name="disable-access-to-azure-data-explorer-from-the-public-ip"></a>パブリック IP から Azure Data Explorer へのアクセスを無効にする

パブリック IP アドレスを使用した Azure Data Explorer へのアクセスを完全に無効にする場合は、NSG で別の受信規則を作成します。 この規則にはより低い[優先順位](/azure/virtual-network/security-overview#security-rules) (より大きい数値) が必要です。 

| **用途**   | **ソース** | **ソース サービス タグ** | **ソース ポート範囲**  | **宛先** | **宛先ポート範囲** | **プロトコル** | **操作** | **優先順位** |
| ---   | --- | --- | ---  | --- | --- | --- | --- | --- |
| インターネットからのアクセスを無効にする | サービス タグ | インターネット | *  | VirtualNetwork | * | Any | 拒否 | 上記の規則よりも大きい数値 |

この規則を使用すると、次の DNS レコード (各サービスのプライベート IP にマップされている) を介してのみ、Azure Data Explorer クラスターに接続できます。
* `private-[clustername].[geo-region].kusto.windows.net` (エンジン)
* `private-ingest-[clustername].[geo-region].kusto.windows.net` (データ管理)

## <a name="expressroute-setup"></a>ExpressRoute セットアップ

ExpressRoute を使用して、オンプレミス ネットワークを Azure 仮想ネットワークに接続できます。 一般的なセットアップでは、Border Gateway Protocol (BGP) セッションを介して既定のルート (0.0.0.0/0) をアドバタイズします。 これにより、Virtual Network からのトラフィックが、トラフィックを破棄する可能性がある顧客のオンプレミス ネットワークに強制的に転送されるため、送信フローが中断される結果となります。 この既定の設定を解決するために、[ユーザー定義ルート (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) を構成でき、次ホップは*インターネット*になります。 UDR は BGP よりも優先されるため、トラフィックはインターネットに送られます。

## <a name="securing-outbound-traffic-with-firewall"></a>ファイアウォールを使用した送信トラフィックのセキュリティ保護

[Azure Firewall](/azure/firewall/overview) または任意の仮想アプライアンスを使用して送信トラフィックのセキュリティを保護し、ドメイン名を制限する場合は、ファイアウォールで次の完全修飾ドメイン名 (FQDN) を許可する必要があります。

```
prod.warmpath.msftcloudes.com:443
gcs.prod.monitoring.core.windows.net:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443,5671
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

> [!NOTE]
> [Azure Firewall](/azure/firewall/overview) を使用している場合は、次のプロパティを使用して**ネットワーク ルール**を追加します。 <br>
> **Protocol**:TCP <br> **[Source Type]\(ソースの種類\)** : IP アドレス <br> **送信元**: * <br> **サービス タグ**:AzureMonitor <br> **宛先ポート**:443

また、非対称ルートの問題を防ぐために、次ホップが*インターネット*である[管理アドレス](#azure-data-explorer-management-ip-addresses)および[正常性監視アドレス](#health-monitoring-addresses)を使用するサブネット上の[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を定義する必要があります。

たとえば、**米国西部**リージョンでは、次の UDR を定義する必要があります。

| 名前 | アドレス プレフィックス | 次ホップ |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | インターネット |
| ADX_Monitoring | 23.99.5.162/32 | インターネット |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターを VNet にデプロイする

Azure Data Explorer クラスターを仮想ネットワークにデプロイするには、「[Azure Data Explorer クラスターを VNet にデプロイする](https://azure.microsoft.com/resources/templates/101-kusto-vnet/)」の Azure Resource Manager テンプレートを使用します。

このテンプレートは、クラスター、仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ、およびパブリック IP アドレスを作成します。
