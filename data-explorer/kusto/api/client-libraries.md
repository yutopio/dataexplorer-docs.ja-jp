---
title: クライアント ライブラリの概要 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクライアント ライブラリの概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 2eb567062402f3c00fce6b12bf81feb507ca6c23
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610178"
---
# <a name="client-libraries-overview"></a>クライアント ライブラリの概要

次の表に、クエリ、インジェスティション、ARM/RP 管理用に用意されているさまざまなライブラリを示します。 これらのライブラリを使用すると、Azure API を使用して、プログラムによって Azure データ エクスプローラーの機能を呼び出す方法をお勧めします。 


|    言語\機能        |    クエリ        |    データの取り込み        |    アーム/RP管理        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .NET        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/1.0.0)         |
|    ネットスタンダード        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [メイブン](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)[・ギットハブ](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [メイブン](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)[・ギットハブ](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2019_01_21/azure-mgmt-kusto)        |
|    Javascript        |             |             |    [故 宮](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    NodeJS        |    [npm](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [npm](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [故 宮](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [ピピ](https://pypi.org/project/azure-kusto-ingest/)    [・ギトハブ](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [ピピ](https://pypi.org/project/azure-kusto-data/)      [・ギトハブ](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [ピピ](https://pypi.org/project/azure-mgmt-kusto/0.3.0/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt/2019-01-21/kusto)        |
|    Ruby        |             |             |    [宝石](https://rubygems.org/gems/azure_mgmt_kusto/versions/0.17.1)         |
|    Powershell        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [パッケージ](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [アズール・クライ](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)         |
|    レスト API        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [故 宮](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>ツールと統合

* ライトインジェスト:[ナゲット](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* ワンクリックインジェスションキット: [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* カフカ:[ギットハブ](https://github.com/Azure/kafka-sink-azure-kustoLogstash)
* ログダッシュ: [GitHub](https://github.com/Azure/logstash-output-kusto) 
* スパーク:[メイブン](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)