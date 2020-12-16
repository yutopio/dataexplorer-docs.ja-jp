---
title: Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)
description: このトピックでは、Azure Data Explorer のクロス プロダクト クエリを作成することによって、Azure Monitor (Application Insights と Log Analytics) でデータのクエリを実行します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 607bcc7b1c0401116a94a9c06ff308a06ad936b7
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371533"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)

Azure Data Explorer では、Azure Data Explorer、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)、[Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) 間のクロス サービス クエリがサポートされています。 Azure Data Explorer のクエリ ツールを使用して Log Analytics または Application Insights ワークスペースに対してクロスサービス クエリでクエリを実行できます。 この記事では、クロスサービス クエリを作成する方法と、Azure Data Explorer の Web UI に Log Analytics または Application Insights のワークスペースを追加する方法について説明します。

Azure Data Explorer のクロスサービス クエリのフローは次のとおりです。

![Azure Data Explorer プロキシのフロー](media/query-monitor-data/query-monitor-workflow.png)

> [!NOTE]
> * Azure Data Explorer クライアント ツールを使用して直接、または Azure Data Explorer クラスターでクエリを実行することによって間接的に、Azure Data Explorer から Azure Monitor データに対してクエリを実行する機能は、プレビュー モードです。
>* 支援が必要な場合は、[クロス サービス クエリ チーム](mailto:adxproxy@microsoft.com)にお問い合わせください。


## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Azure Data Explorer クライアント ツールに Log Analytics/Application Insights ワークスペースを追加する

Log Analytics または Application Insights のワークスペースを Azure Data Explorer クライアント ツールに追加して、クラスターのクロスサービス クエリを有効にします。

1. Log Analytics または Application Insights クラスターに接続する前に、Azure Data Explorer ネイティブ クラスター (*help* クラスターなど) が左側のメニューに表示されていることを確認します。

    ![Azure Data Explorer ネイティブ クラスター](media/query-monitor-data/web-ui-help-cluster.png)

1. Azure Data Explorer の UI (https://dataexplorer.azure.com/clusters) ) で、 **[クラスターの追加]** を選択します。

1. **[クラスターの追加]** ウィンドウで、LA または AI クラスターの URL を追加します。

    * LA の場合: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI の場合: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

1. **[追加]** を選択します。

    ![クラスターを追加する](media/query-monitor-data/add-cluster.png)

    >[!TIP]
    >複数の Log Analytics または Application Insights ワークスペースへの接続を追加する場合は、それぞれに異なる名前を付けます。 そうしないと、左側のウィンドウですべてが同じ名前になります。

1. 接続が確立されると、Log Analytics または Application Insights のワークスペースが、ネイティブの Azure Data Explorer クラスターとともに左側のペインに表示されます。

    ![Log Analytics クラスターと Azure Data Explorer クラスター](media/query-monitor-data/la-adx-clusters.png)

> [!NOTE]
> マップできる Azure Monitor ワークスペースの数は、100 に制限されています。

## <a name="run-queries"></a>クエリを実行する

Kusto クエリをサポートするクライアント ツールを使用してクエリを実行できます。例:Kusto Explorer、Azure Data Explorer の Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Lens、REST API。

> [!NOTE]
> クロスサービス クエリ機能は、データ取得のみに使用されます。 詳細については、「[関数のサポート](#function-supportability)」を参照してください。

> [!TIP]
> * データベースは、クロスサービス クエリで指定したリソースと同じ名前にする必要があります。 名前は大文字と小文字が区別されます。
> * クロスサービス クエリでは、Application Insights アプリと Log Analytics ワークスペースの名前付けが正しいことを確認してください。
> * 名前に特殊文字が含まれている場合は、クロスサービス クエリで URL エンコードに置き換えられます。
> * 名前に [KQL 識別子の名前規則](kusto/query/schema-entities/entity-names.md)を満たしていない文字が含まれている場合は、ダッシュ **-** 文字で置き換えられます。

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Azure Data Explorer クライアント ツールからの Log Analytics/Application Insights ワークスペースでの直接クエリ

Azure Data Explorer クライアント ツールから Log Analytics または Application Insights のワークスペースでクエリを実行できます。 

1. 左側のペインでワークスペースが選択されていることを確認します。

1. 次のクエリを実行します。

```kusto
Perf | take 10 // Demonstrate cross-service query on the Log Analytics workspace
```

![Log Analytics ワークスペースにクエリを実行する](media/query-monitor-data/query-la.png)

### <a name="cross-query-of-your-log-analytics-or-application-insights-workspace-and-the-azure-data-explorer-native-cluster"></a>Log Analytics または Application Insights のワークスペースと Azure Data Explorer ネイティブ クラスターのクロス クエリ

クロス クラスター サービス クエリを実行する場合は、左側のペインで Azure Data Explorer ネイティブ クラスターが選択されていることを確認してください。 次の例では、Azure Data Explorer クラスター テーブルと Log Analytics ワークスペースを (`union` を使用して) 結合する方法を示します。

次のクエリを実行します。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Azure Data Explorer からのクロス サービス クエリ](media/query-monitor-data/cross-query.png)](media/query-monitor-data/cross-query.png#lightbox)

> [!TIP]
> union の代わりに [`join` 演算子](kusto/query/joinoperator.md)を使用するには、それを Azure Data Explorer ネイティブ クラスターに対して実行するための [`hint`](kusto/query/joinoperator.md#join-hints) が必要になる場合があります。

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>一方のテナントの Azure Data Explorer クラスターのデータを他方の Azure Monitor リソースと結合する

サービス間のクロス テナント クエリはサポートされていません。 両方のリソースにまたがるクエリを実行するためには、1 つのテナントにサインインします。

Azure Data Explorer リソースがテナント 'A' にあり、Log Analytics ワークスペースがテナント 'B' にある場合は、次の 2 つの方法のいずれかを使用します。

1. Azure Data Explorer を使用すると、異なるテナントにプリンシパルのロールを追加できます。 Azure Data Explorer クラスターにテナント 'B' のユーザー ID を許可されているユーザーとして追加します。 Azure Data Explorer クラスター上の *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* プロパティにテナント 'B' が含まれていることを検証します。 テナント 'B' でクロスクエリを完全に実行します。

2. [Lighthouse](/azure/lighthouse/) を使用して、Azure Monitor リソースをテナント 'A' に射影します。

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>さまざまなテナントから Azure Data Explorer クラスターに接続する

Kusto Explorer では、ユーザー アカウントが最初に属しているテナントに自動的にサインインされます。 同じユーザー アカウントを使用して他のテナントのリソースにアクセスするには、接続文字列に `tenantId` を明示的に指定する必要があります: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>関数のサポート

Azure Data Explorer のクロスサービス クエリでは、Application Insights と Log Analytics の両方の関数がサポートされています。
この機能により、クロス クラスター クエリで Azure Monitor の表形式関数を直接参照できます。
次のコマンドがクロスサービス クエリでサポートされています。

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

次の図は、Azure Data Explorer の Web UI から表形式関数にクエリを実行する例を示しています。
関数を使用するには、[クエリ] ウィンドウで名前を実行します。

  [ ![Azure Data Explorer の Web UI から表形式関数のクエリを実行する](media/query-monitor-data/function-query.png)](media/query-monitor-data/function-query.png#lightbox)

## <a name="additional-syntax-examples"></a>その他の構文例

Application Insights または Log Analytics クラスターを呼び出す場合は、次の構文オプションを使用できます。

|構文の説明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| このサブスクリプションで定義されているリソースのみを含むクラスター内のデータベース (**クロス クラスター クエリの場合に推奨**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| このサブスクリプション内のすべてのアプリ/ワークスペースを含むクラスター    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|サブスクリプション内のすべてのアプリ/ワークスペースを含み、このリソース グループのメンバーであるクラスター    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|このサブスクリプションで定義されているリソースのみを含むクラスター      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>次のステップ

[クエリを作成する](write-queries.md)