---
title: クエリの制限 - Azure Data Explorer
description: この記事では、Azure Data Explorer のクエリ制限について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 0a25e0a4354798780b652861ba93494135b6d581
ms.sourcegitcommit: fd034cf3e3440dcbbbb8494eb4914572055afcee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759713"
---
# <a name="query-limits"></a>クエリの制限

Kusto は、大規模なデータ セットをホストし、関連するすべてのデータをメモリ内に保持することでクエリを満たすことを試行するアドホック クエリ エンジンです。
クエリによってサービス リソースが際限なく独占されるという固有のリスクがあります。 Kusto には、既定のクエリ制限という形でいくつかの組み込みの保護が用意されています。 これらの制限を削除することを検討している場合は、まず、その結果として実際に何かメリットがあるかどうかを判断してください。

## <a name="limit-on-query-concurrency"></a>クエリの同時実行に対する制限

**クエリの同時実行** は、クラスターによって、同時に実行される複数のクエリに課される制限です。

* クエリの同時実行制限の既定値は、実行されている SKU クラスターによって変わり、`Cores-Per-Node x 10` として計算されます。
  * たとえば、各マシンに 16 個の仮想コアがある D14v2 SKU 上に設定されたクラスターの場合、既定のクエリ同時実行制限は `16 cores x10 = 160` です。
* `default` ワークロード グループの[要求レート制限ポリシー](../management/request-rate-limit-policy.md)を構成すると、既定値を変更できます。
  * クラスター上で同時に実行できるクエリの実際の数は、さまざまな要因によって変わります。 最も重要な要因は、クラスター SKU、クラスターの使用可能なリソース、クエリ パターンです。 クエリ調整ポリシーは、運用環境に似たクエリ パターンで実行される負荷テストに基づいて構成できます。

## <a name="limit-on-result-set-size-result-truncation"></a>結果セット サイズに対する制限 (結果の切り捨て)

**結果の切り捨て** は、クエリによって返される結果セットに既定で設定される制限です。 Kusto では、クライアントに返されるレコード数が **500,000** に制限され、そのレコードの全体的なデータ サイズは **64 MB** に制限されています。 これらの制限を超えた場合、クエリは "部分的なクエリ エラー" で失敗します。 全体のデータ サイズを超えると、次のメッセージの例外が生成されます。

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

レコード数を超えると、次のような例外が発生して失敗します。

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

このエラーを処理するには、いくつかの方法があります。

* 必要なデータのみを返すようにクエリを変更することで、結果セットのサイズを小さくします。 この戦略は、最初に失敗したクエリが "広範" である場合に役立ちます。 たとえば、不要なデータ列がクエリに反映されていない場合です。
* 集計など、クエリ後の処理をクエリ自体に移動することで、結果セットのサイズを小さくします。 この戦略は、クエリの出力が別の処理システムに送られ、そこで他の集計が行われるシナリオで役立ちます。
* サービスから大量のデータ セットをエクスポートする場合は、クエリから[データのエクスポート](../management/data-export/index.md)の使用に切り替えます。
* 次に示す `set` ステートメントまたは[クライアント要求プロパティ](../api/netfx/request-properties.md)のフラグを使用して、このクエリ制限を抑制するようにサービスに指示します。

クエリによって生成される結果セットのサイズを小さくするには、次のような方法があります。

* [summarize 演算子](../query/summarizeoperator.md)グループを使用して、クエリ出力内の類似レコードを集計します。 [任意の集計関数](../query/any-aggfunction.md)を使用して、いくつかの列をサンプリングすることもできます。
* [take 演算子](../query/takeoperator.md)を使用してクエリ出力をサンプリングします。
* [substring 関数](../query/substringfunction.md)を使用して、幅の広いフリーテキスト列をトリミングします。
* [project 演算子](../query/projectoperator.md)を使用して、結果セットから不要な列を削除します。

`notruncation` 要求オプションを使用して、結果の切り捨てを無効にすることができます。
ただし、何らかの形式の制限を設定しておくことをお勧めします。

例:

```kusto
set notruncation;
MyTable | take 1000000
```

`truncationmaxsize` (バイト単位の最大データ サイズ、既定値は 64 MB) と `truncationmaxrecords` (最大レコード数、既定値は 500,000) の値を設定することで、結果の切り捨てをより細かく制御することもできます。 たとえば、次のクエリでは、1,105 件のレコードまたは 1 MB のいずれかを超えたときに結果の切り捨てが発生するように設定されます。

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

結果の切り捨て制限を削除することは、大量のデータが Kusto から移動されることを意味します。

結果の切り捨て制限を削除できるのは、`.export` コマンドを使用してエクスポートする目的がある場合、または後で集計する場合です。 後で集計する場合は、Kusto を使用して集計することを検討してください。

Kusto には、呼び出し元にストリーミングすることで、"非常に大規模な" 結果を処理できるようにするクライアント ライブラリが多数用意されています。 このようなライブラリのいずれかを使用して、ストリーミング モードに構成します。 たとえば、.NET Framework クライアント (Microsoft.Azure.Kusto.Data) を使用して、接続文字列のストリーミング プロパティを *true* に設定するか、*ExecuteQueryV2Async()* の呼び出しを使用して常に結果をストリーミングするようにします。

結果の切り捨ては、クライアントに返される結果のストリームだけでなく、既定で適用されます。 また、既定で、あるクラスターから別のクラスターにクロスクラスター クエリで発行されるサブクエリにも適用され、同様の効果があります。

### <a name="setting-multiple-result-truncation-properties"></a>複数の結果の切り捨てプロパティの設定

`set` ステートメントを使用する場合、または[クライアント要求のプロパティ](../api/netfx/request-properties.md)でフラグを指定する場合は、次のことが適用されます。

* `notruncation` が設定されて、`truncationmaxsize`、`truncationmaxrecords`、`query_take_max_records` のいずれかも設定されている場合、`notruncation` は無視されます。
* `truncationmaxsize`、`truncationmaxrecords` または `query_take_max_records` が複数回設定されている場合は、各プロパティの "*小さい*" 方の値が適用されます。

## <a name="limit-on-memory-per-iterator"></a>反復子あたりのメモリに対する制限

**結果セット反復子あたりの最大メモリ** は、"暴走" クエリから保護するために Kusto に使用されるもう 1 つの制限です。 この制限は要求オプション `maxmemoryconsumptionperiterator` で表され、1 つのクエリ プランの結果セット反復子に保持できるメモリ量の上限が設定されます。 この制限は、`join` など、性質的にストリーミングされない特定の反復子に適用されます。この状況になると、次のようなエラー メッセージが返されます。

```
The ClusterBy operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete E_RUNAWAY_QUERY.

The DemultiplexedResultSetCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The ExecuteAndCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Sort operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Summarize operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNestedAggregator operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNested operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).
```

既定では、この値は 5 GB に設定されています。 この値は、マシンの物理メモリの半分まで増やすことができます。

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

多くの場合、データ セットをサンプリングすることで、この制限を超えないようにすることができます。 次の 2 つのクエリは、サンプリングの実行方法を示しています。 1 つ目は統計サンプリングです。これには乱数ジェネレーターが使用されます。 2 つ目は決定論的サンプリングです。これは、データ セットの何らかの列 (通常は ID) をハッシュするという方法で行われます。

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

`maxmemoryconsumptionperiterator` が複数回設定されている場合 (たとえばクライアント要求のプロパティと `set` ステートメントの両方を使用)、低い方の値が適用されます。


## <a name="limit-on-memory-per-node"></a>ノードあたりのメモリに対する制限

**ノードあたりのクエリあたりの最大メモリ** は、"暴走" クエリから保護するために使用されるもう 1 つの制限です。 この制限は要求オプション `max_memory_consumption_per_query_per_node` で表され、特定のクエリに対して 1 つノードで使用できるメモリ量の上限が設定されます。

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

`max_memory_consumption_per_query_per_node` が複数回設定されている場合 (たとえばクライアント要求のプロパティと `set` ステートメントの両方を使用)、低い方の値が適用されます。

## <a name="limit-on-accumulated-string-sets"></a>累積文字列セットに対する制限

さまざまなクエリ演算では、Kusto を使用して文字列値を "収集" し、結果の生成を開始する前にそれらを内部的にバッファーする必要があります。 このような累積文字列セットは、保持できる項目のサイズと数に制限があります。 さらに、個々の文字列には超えることができない特定の制限があります。
このような制限のいずれかを超えると、次のいずれかのエラーが発生します。

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

現在、文字列セットの最大サイズを大きくするスイッチはありません。
回避策として、バッファーする必要のあるデータ量を減らすようにクエリを書き換えてください。 結合や集計などの演算子に使用する前に、不要な列を除外することができます。 または、[クエリのシャッフル](../query/shufflequery.md)戦略を使用できます。

## <a name="limit-execution-timeout"></a>実行タイムアウトの制限

**サーバー タイムアウト** は、すべての要求に適用されるサービス側のタイムアウトです。
実行中の要求 (クエリと制御コマンド) のタイムアウトは、Kusto の複数のポイントに適用されます。

* クライアント ライブラリ (使用されている場合)
* 要求を受け入れるサービス エンドポイント
* 要求を処理するサービス エンジン

既定では、クエリのタイムアウトは 4 分、制御コマンドでは 10 分に設定されています。 この値は、必要に応じて増やすことができます (上限は 1 時間)。

* Kusto.Explorer を使用してクエリを実行する場合は、 **[Tools]\(ツール\)** &gt; **[Options]\(オプション\)** _ &gt; _ *[Connections]\(接続\)* * &gt; **[Query Server Timeout]\(クエリ サーバーのタイムアウト\)** を使用します。
* プログラムによって、`servertimeout` クライアント要求プロパティ (型 `System.TimeSpan` の値) を最大 1 時間に設定します。

**タイムアウトに関する注意事項**

* クライアント側では、作成された要求から応答がクライアントに到着し始めるまでのタイムアウトが適用されます。 クライアントでペイロードの読み取りにかかる時間は、タイムアウトの一部として扱われません。 これは、呼び出し元がストリームからデータをプルする速度によって変わります。
* また、クライアント側では、使用される実際のタイムアウト値は、ユーザーが要求したサーバー タイムアウト値よりもわずかに高くなります。 ネットワークの待機時間を考慮に入れるために、この差があります。
* 最大許容要求タイムアウトを自動的に使用するには、クライアント要求プロパティ `norequesttimeout` を `true` に設定します。

## <a name="limit-on-query-cpu-resource-usage"></a>クエリの CPU リソース使用率に対する制限

Kusto を使用すると、クエリを実行して、クラスターと同じ数の CPU リソースを使用できます。 複数のクエリが実行されている場合は、クエリ間で公平なラウンド ロビンが試行されます。 この方法では、アドホック クエリで最高のパフォーマンスが得られます。
また、特定のクエリに使用される CPU リソースを制限する場合もあります。 たとえば、"バックグラウンド ジョブ" を実行する場合、システムによって高い待機時間が許容され、同時アドホック クエリの優先度が高くなる可能性があります。

Kusto は、クエリの実行時に 2 つの[クライアント要求プロパティ](../api/netfx/request-properties.md)を指定することをサポートしています。
そのプロパティは、*query_fanout_threads_percent* と *query_fanout_nodes_percent* です。
どちらのプロパティも既定値は最大値 (100) の整数ですが、クエリによっては他の値に減らすこともできます。

1 つ目 *query_fanout_threads_percent* を使用すると、スレッドの使用に対するファンアウト係数を制御できます。
このプロパティが 100% に設定されている場合、クラスターによって各ノードにすべての CPU が割り当てられます。 たとえば、Azure D14 ノードにデプロイされたクラスター上の 16 個の CPU です。
このプロパティが 50% に設定されている場合は CPU の半分が使用されます。その他の値も同様です。
数値は CPU 全体に切り上げられるため、プロパティ値を 0 に設定しても問題ありません。

2 つ目の *query_fanout_nodes_percent* を使用すると、サブクエリの分散演算ごとに使用するクラスター内のクエリ ノード数を制御できます。
機能は同様です。

`query_fanout_nodes_percent` または `query_fanout_threads_percent` が複数回設定されている場合 (たとえばクライアント要求のプロパティと `set` ステートメントの両方を使用)、各プロパティの低い方の値が適用されます。

## <a name="limit-on-query-complexity"></a>クエリの複雑さに対する制限

クエリの実行時に、クエリ テキストはクエリを表す関係演算子のツリーに変換されます。
ツリーの深さが数千レベルの内部しきい値を超えると、複雑すぎて処理できないクエリと見なされて失敗し、エラー コードが返されます。 このエラーは、関係演算子ツリーが制限を超えていることを示します。
多数のバイナリ演算子が連結されているクエリがあるため、制限を超えています。 例:

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

この特定のケースでは、[`in()`](../query/inoperator.md) 演算子を使用してクエリを書き直してください。

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
