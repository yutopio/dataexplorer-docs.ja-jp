---
title: クエリ結果のキャッシュ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ結果キャッシュ機能について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 24ab3cb3e423e3ab6b77f09f2c216feb07ae0d0f
ms.sourcegitcommit: f134d51e52504d3ca722bdf6d33baee05118173a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563310"
---
# <a name="query-results-cache"></a>クエリ結果のキャッシュ

Kusto には、クエリ結果のキャッシュが含まれています。 クエリを発行するときに、キャッシュされた結果を取得するように選択できます。 クエリの結果がキャッシュによって返される場合は、クエリのパフォーマンスが向上し、リソースの消費が少なくなります。 ただし、このパフォーマンスは、結果の一部の "staleness" によって発生します。

## <a name="use-the-cache"></a>キャッシュの使用

クエリ `query_results_cache_max_age` 結果キャッシュを使用するには、オプションをクエリの一部として設定します。 このオプションは、クエリテキストまたはクライアント要求プロパティで設定できます。 例:

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

オプション値は、 `timespan` クエリの開始時刻から計測される、結果キャッシュの最大の "age" を示すです。 Set timespan 以外では、キャッシュエントリは互換性のために残されており、再度使用されることはありません。 値を0に設定することは、オプションを設定しないことと同じです。

## <a name="compatibility-between-queries"></a>クエリ間の互換性

### <a name="identical-queries"></a>同一のクエリ

クエリ結果のキャッシュでは、以前にキャッシュされたクエリに対して "同一" と見なされたクエリに対してのみ結果が返されます。 次の条件がすべて満たされている場合、2つのクエリは同一であると見なされます。

* 2つのクエリは、(UTF-8 文字列として) 同じ表現を持ちます。
* 2つのクエリが同じデータベースに対して行われます。
* 2つのクエリは、同じ [クライアント要求のプロパティ](../api/netfx/request-properties.md)を共有します。 キャッシュの目的では、次のプロパティは無視されます。
   * [ClientRequestId](../api/netfx/request-properties.md#clientrequestid-x-ms-client-request-id)
   * [Application](../api/netfx/request-properties.md#application-x-ms-app)
   * [ユーザー](../api/netfx/request-properties.md#user-x-ms-user)

### <a name="incompatible-queries"></a>互換性のないクエリ

次の条件のいずれかに該当する場合、クエリ結果はキャッシュされません。
 
* このクエリは、 [RestrictedViewAccess](../management/restrictedviewaccesspolicy.md) ポリシーが有効になっているテーブルを参照します。
* このクエリは、 [Rowlevelsecurity](../management/rowlevelsecuritypolicy.md) ポリシーが有効になっているテーブルを参照します。
* クエリでは、次の関数のいずれかを使用します。
    * [current_principal](current-principalfunction.md)
    * [current_principal_details](current-principal-detailsfunction.md)
    * [current_principal_is_member_of](current-principal-ismemberoffunction.md)
* このクエリは、 [外部テーブル](schema-entities/externaltables.md) または [外部データ](externaldata-operator.md)にアクセスします。
* このクエリでは、 [evaluate plugin](evaluateoperator.md) 演算子を使用します。

## <a name="no-valid-cache-entry"></a>有効なキャッシュエントリがありません

時間制約を満たすキャッシュされた結果が見つからなかった場合、またはキャッシュ内の "同一" クエリからキャッシュされた結果がない場合は、次の場合に限り、クエリが実行され、その結果がキャッシュされます。 

* クエリの実行は正常に完了し、
* クエリ結果のサイズが 16 MB を超えていません。

## <a name="results-from-the-cache"></a>キャッシュの結果

クエリ結果がキャッシュから提供されているかどうかは、サービスによってどのように示されますか。
Kusto は、クエリに応答するときに、列と列を含む追加の [Extendedproperties](../api/rest/response.md) 応答テーブルを送信し `Key` `Value` ます。
キャッシュされたクエリ結果には、そのテーブルに追加の行が追加されます。
* 行の列には `Key` 、という文字列が含まれます。 `ServerCache`
* 行の `Value` 列には、次の2つのフィールドを持つプロパティバッグが含まれます。
   * `OriginalClientRequestId` -元の要求の [Clientrequestid](../api/netfx/request-properties.md#clientrequestid-x-ms-client-request-id)を指定します。
   * `OriginalStartedOn` -元の要求の実行開始時刻を指定します。

## <a name="distribution"></a>Distribution

キャッシュはクラスターノードによって共有されていません。 すべてのノードは、専用のキャッシュを独自のプライベートストレージに保持します。 2つの同一のクエリが異なるノードに配置されると、クエリが実行され、両方のノードにキャッシュされます。 このプロセスは、 [弱い整合性](../concepts/queryconsistency.md) が使用されている場合に発生する可能性があります。

## <a name="management"></a>管理

次の管理コマンドと可観測性コマンドがサポートされています。

* [キャッシュを表示](../management/show-query-results-cache-command.md)します。
* [キャッシュをクリア](../management/clear-query-results-cache-command.md)します。

## <a name="capacity"></a>容量

現在、キャッシュ容量はクラスターノードごとに 1 GB に固定されています。
削除ポリシーは LRU です。
