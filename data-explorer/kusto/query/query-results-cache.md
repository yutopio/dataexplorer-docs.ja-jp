---
title: クエリ結果のキャッシュ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ結果キャッシュ機能について説明します。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3c1e1eec2bb0ad4d856ca690039dea9aedd78e8f
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943134"
---
# <a name="query-results-cache"></a>クエリ結果のキャッシュ

Kusto には、クエリ結果のキャッシュが含まれています。 クエリを発行するときに、キャッシュされた結果を取得することを示すことができます。 クエリの結果がキャッシュによって返される場合、クエリのパフォーマンスが向上し、結果に "staleness" を犠牲にしてリソースの消費量が少なくなります。

## <a name="indicating-willingness-to-use-the-cache"></a>キャッシュを使用する意思を示す

クエリ `query_results_cache_max_age` の一部としてオプションを設定して、クエリ結果のキャッシュを使用する意欲を示します。 このオプションは、クエリテキストまたはクライアント要求プロパティのどちらかで設定できます。 次に例を示します。

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

オプション値は、 `timespan` クエリの開始時刻から計測される、結果キャッシュの最大の "age" を示すです。 Set timespan 以外では、キャッシュエントリは互換性のために残されており、再度使用されることはありません。 値を0に設定することは、オプションを設定しないことと同じです。

## <a name="how-compatibility-between-queries-is-determined"></a>クエリ間の互換性を確認する方法

Query_results_cache は、以前にキャッシュされたクエリと "同一" と見なされるクエリに対してのみ結果を返します。 次の条件がすべて満たされている場合、2つのクエリは同一であると見なされます。

* 2つのクエリは、(UTF-8 文字列として) 同じ表現を持ちます。

* 2つのクエリが同じデータベースに対して行われます。

* 2つのクエリは、同じ[クライアント要求のプロパティ](../api/netfx/request-properties.md)を共有します。 キャッシュの目的では、次のプロパティは無視されます。
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [アプリケーション](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)。
   * [User](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

## <a name="if-the-query-results-cache-cant-find-a-valid-cache-entry"></a>クエリ結果キャッシュで有効なキャッシュエントリが見つからない場合

時間制約を満たすキャッシュされた結果が見つからなかった場合、またはキャッシュ内の "同一" クエリからキャッシュされた結果がない場合は、次の場合に限り、クエリが実行され、その結果がキャッシュされます。 

* クエリの実行は正常に完了し、
* クエリ結果のサイズが 16 MB を超えていません。

## <a name="how-the-service-indicates-that-the-query-results-are-being-served-from-the-cache"></a>クエリ結果がキャッシュから提供されていることをサービスが示す方法

Kusto は、クエリに応答するときに、列と列を含む追加の[Extendedproperties](../api/rest/response.md)応答テーブルを送信し `Key` `Value` ます。
キャッシュされたクエリ結果には、そのテーブルに追加の行が追加されます。
* 行の列には `Key` 、という文字列が含まれます。`ServerCache`
* 行の `Value` 列には、次の2つのフィールドを持つプロパティバッグが含まれます。
   * `OriginalClientRequestId`-元の要求の[Clientrequestid](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)を指定します。
   * `OriginalStartedOn`-元の要求の実行開始時刻を指定します。

## <a name="distribution"></a>Distribution

キャッシュはクラスターノードによって共有されていません。 すべてのノードは、専用のキャッシュを独自のプライベートストレージに保持します。 2つの同一のクエリが異なるノードに配置されると、クエリが実行され、両方のノードにキャッシュされます。 このプロセスは、[弱い整合性](../concepts/queryconsistency.md)が使用されている場合に発生する可能性があります。

## <a name="management"></a>管理

次の管理コマンドと可観測性コマンドがサポートされています。

* [キャッシュを表示](../management/show-query-results-cache-command.md)します。
* [キャッシュをクリア](../management/clear-query-results-cache-command.md)します。

## <a name="capacity"></a>容量

現在、キャッシュ容量はクラスターノードごとに 1 GB に固定されています。
削除ポリシーは LRU です。
