---
title: クエリの制限-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ制限について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: d0f815cd523e0e53111e791d8faaaf6c37c7bb7b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252844"
---
# <a name="query-limits"></a>クエリの制限

Kusto は、大規模なデータセットをホストし、関連するすべてのデータをメモリ内に保持することによってクエリを満たすことを試行するアドホッククエリエンジンです。
クエリによってサービスリソースが境界なしで独占されるという固有のリスクがあります。 Kusto には、既定のクエリ制限という形式で、組み込みの保護機能が多数用意されています。 これらの制限を削除することを検討している場合は、まず、実際に値を取得するかどうかを判断します。

## <a name="limit-on-query-concurrency"></a>クエリの同時実行の制限

**クエリの同時実行性**  は、クラスターが同時に実行されている多数のクエリに対して実行する制限です。

* クエリの同時実行の制限の既定値は、実行されている SKU クラスターによって異なり、次のように計算されます `Cores-Per-Node x 10` 。
  * たとえば、D14v2 SKU で設定されているクラスターの場合、各マシンに16個の仮想コアがあるため、クエリの同時実行の既定の制限はに `16 cores x10 = 160` なります。
* 既定値は、 [クエリの制限ポリシー](../management/query-throttling-policy.md)を構成することによって変更できます。 
  * クラスターで同時に実行できるクエリの実際の数は、さまざまな要因によって異なります。 最も重要な要因は、クラスターの SKU、クラスターの使用可能なリソース、およびクエリパターンです。 クエリの調整ポリシーは、運用環境に似たクエリパターンで実行されるロードテストに基づいて構成できます。

## <a name="limit-on-result-set-size-result-truncation"></a>結果セットのサイズの制限 (結果の切り捨て)

**結果の切り捨て** は、クエリによって返される結果セットに対して既定で設定される制限です。 Kusto によって、クライアントに返されるレコードの数が **50万**に制限され、それらのレコードのデータサイズ全体が **64 MB**に制限されます。 これらの制限を超えた場合、クエリは "部分的なクエリエラー" で失敗します。 全体のデータサイズを超えると、次のメッセージで例外が生成されます。

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

レコード数を超えると、次のような例外が発生して失敗します。

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

このエラーを処理するためのさまざまな方法があります。

* 興味深いデータのみを返すようにクエリを変更して、結果セットのサイズを小さくします。 この方法は、最初に失敗したクエリが "ワイド" である場合に便利です。 たとえば、クエリでは必要のないデータ列が射影されません。
* 集計などのクエリ処理をクエリ自体に移動することによって、結果セットのサイズを小さくします。 この方法は、クエリの出力が別の処理システムに送られ、さらに集計が行われるシナリオで役立ちます。
* 大量のデータセットをサービスからエクスポートする場合は、クエリから [データエクスポート](../management/data-export/index.md) を使用するように切り替えます。
* `set`次に示すステートメントまたは[クライアント要求プロパティ](../api/netfx/request-properties.md)のフラグを使用して、このクエリ制限を抑制するようにサービスに指示します。

クエリによって生成される結果セットのサイズを減らす方法は次のとおりです。

* [集計演算子](../query/summarizeoperator.md)グループを使用して、クエリ出力の類似レコードを集計します。 [任意の集計関数](../query/any-aggfunction.md)を使用していくつかの列をサンプリングする可能性があります。
* クエリ出力をサンプリングするには、 [take 演算子](../query/takeoperator.md) を使用します。
* [部分文字列関数](../query/substringfunction.md)を使用すると、幅の広いフリーテキスト列をトリミングできます。
* 結果セットから不要な列を削除するには、 [project 演算子](../query/projectoperator.md) を使用します。

要求オプションを使用して、結果の切り捨てを無効にすることができ `notruncation` ます。
何らかの形式の制限を設定しておくことをお勧めします。

次に例を示します。

```kusto
set notruncation;
MyTable | take 1000000
```

また、の値 `truncationmaxsize` (バイト単位の最大データサイズ、既定値は 64 MB) および `truncationmaxrecords` (最大レコード数、既定値は 50万) を設定して、結果の切り捨てをより細かく制御することもできます。 たとえば、次のクエリでは、結果の切り捨てが1105レコードまたは 1 MB のいずれかで発生するように設定されています。

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

結果の切り捨て制限を削除することは、Kusto からに一括データを移動することを意味します。

結果の切り捨て制限は、エクスポートのためにコマンドを使用するか、後で集計するために削除でき `.export` ます。 後の集計を選択した場合は、Kusto を使用して集計することを検討してください。

Kusto は、呼び出し元にストリーミングすることで、"無限に大きい" 結果を処理できるクライアントライブラリを多数提供します。 これらのライブラリのいずれかを使用して、ストリーミングモードに構成します。 たとえば、.NET Framework クライアント (ExecuteQueryV2Async) を使用して、接続文字列の streaming プロパティを *true*に設定するか、常に結果をストリーミングする *()* 呼び出しを使用します。

結果の切り捨ては、クライアントに返される結果ストリームだけでなく、既定で適用されます。 また、クラスター化されていないクエリで別のクラスターに発行されるサブクエリにも、同じような影響を及ぼす既定で適用されます。

## <a name="limit-on-memory-per-iterator"></a>反復子ごとのメモリの制限

**結果セット反復子ごとの最大メモリ** は、Kusto が "ランナウェイ" クエリに対して保護するために使用するもう1つの制限です。 この制限は、要求オプションによって表さ `maxmemoryconsumptionperiterator` れ、1つのクエリプランの結果セット反復子が保持できるメモリの量の上限を設定します。 この制限は、などの性質によってストリーミングされていない特定の反復子に適用さ `join` れます。この状況が発生すると、次のようなエラーメッセージが返されます。

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

既定では、この値は 5 GB に設定されています。 この値は、コンピューターの物理メモリの半分まで増やすことができます。

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

多くの場合、この制限を超えると、データセットをサンプリングすることで回避できます。 次の2つのクエリは、サンプリングを実行する方法を示しています。 1つ目は、乱数ジェネレーターを使用する統計サンプリングです。 2つ目は、データセットからいくつかの列 (通常は ID) をハッシュすることによって行われる決定的なサンプリングです。

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>ノードあたりのメモリの制限

**ノードあたりのクエリあたりの最大メモリ容量** は、"ランナウェイ" クエリから保護するために使用されるもう1つの制限です。 この制限は、要求オプションによって表され `max_memory_consumption_per_query_per_node` 、特定のクエリに対して1つのノードで使用できるメモリの量に上限を設定します。

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>累積文字列セットの制限

さまざまなクエリ操作では、Kusto は、結果の生成を開始する前に、文字列値を "収集" して内部的にバッファーする必要があります。 これらの累積文字列セットのサイズと保持できる項目の数は制限されています。 また、各文字列は特定の制限を超えることはできません。
これらの制限のいずれかを超えると、次のいずれかのエラーが発生します。

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

現在、文字列セットの最大サイズを大きくするスイッチはありません。
回避策として、クエリを書き直して、バッファーする必要があるデータの量を減らします。 結合や集計などの演算子によって使用される前に、不要な列を射影することができます。 また、 [シャッフルクエリ](../query/shufflequery.md) 戦略を使用することもできます。

## <a name="limit-execution-timeout"></a>実行タイムアウトの制限

**サーバータイムアウト** は、すべての要求に適用されるサービス側のタイムアウトです。
実行中の要求 (クエリおよび制御コマンド) のタイムアウトは、Kusto の複数のポイントに適用されます。

* クライアントライブラリ (使用されている場合)
* 要求を受け入れるサービスエンドポイント
* 要求を処理するサービスエンジン

既定では、クエリのタイムアウトは4分、制御コマンドでは10分に設定されています。 この値は、必要に応じて増やすことができます (1 時間に上限)。

* Kusto. エクスプローラーを使用してクエリを実行する場合は、**ツール** &gt; **オプション**の *  &gt; **接続** &gt; **クエリサーバーのタイムアウト**を使用します。
* プログラムによって、 `servertimeout` クライアント要求プロパティを、型の値 `System.TimeSpan` (最大1時間) に設定します。

**タイムアウトに関する注意事項**

* クライアント側では、応答がクライアントに到着するまでの間、作成された要求からタイムアウトが適用されます。 クライアントでのペイロードの読み取りにかかる時間は、タイムアウトの一部として扱われません。 これは、呼び出し元がストリームからデータをプルする速度に依存します。
* また、クライアント側では、実際に使用されるタイムアウト値は、ユーザーによって要求されたサーバーのタイムアウト値よりも若干高くなります。 この違いは、ネットワーク待機時間を許容することです。
* [許可された最大要求タイムアウト] を自動的に使用するには、[クライアント要求] プロパティ `norequesttimeout` をに設定 `true` します。

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here since it shouldn't be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>クエリ CPU リソース使用率の制限

Kusto を使用すると、クエリを実行し、クラスターと同じ CPU リソースを使用できます。 複数のクエリが実行されている場合、クエリ間で公平なラウンドロビンを試行します。 この方法では、アドホッククエリに最適なパフォーマンスが得られます。
それ以外の場合は、特定のクエリに使用される CPU リソースを制限することもできます。 たとえば、"バックグラウンドジョブ" を実行すると、同時アドホッククエリの優先度を高くするためにシステムの待機時間が長くなる可能性があります。

Kusto は、クエリの実行時に2つの [クライアント要求プロパティ](../api/netfx/request-properties.md) を指定することをサポートしています。 プロパティは  *query_fanout_threads_percent* および *query_fanout_nodes_percent*です。
どちらのプロパティも、既定値が最大値 (100) の整数ですが、特定のクエリが他の値に対して縮小される場合があります。 

1つ目の *query_fanout_threads_percent*は、スレッド使用のファンアウト factor を制御します。 100% の場合、クラスターは各ノードのすべての Cpu を割り当てます。 たとえば、Azure D14 ノードにデプロイされたクラスター上の16個の Cpu。 50% の場合、Cpu の半分が使用されます。 数値は CPU 全体に切り上げられるので、0に設定するのが安全です。 2番目の *query_fanout_nodes_percent*は、サブクエリのディストリビューション操作ごとに使用する、クラスター内のクエリノードの数を制御します。 同様の方法で機能します。

## <a name="limit-on-query-complexity"></a>クエリの複雑さに対する制限

クエリの実行中は、クエリを表す関係演算子のツリーにクエリテキストが変換されます。
ツリーの深さが数千レベルの内部しきい値を超えた場合、クエリは複雑すぎると見なされ、処理に失敗し、エラーコードが表示されます。 このエラーは、関係演算子ツリーが制限を超えていることを示します。
多数のバイナリ演算子が連結されているクエリがあるため、制限を超えています。 次に例を示します。

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

この特定のケースでは、演算子を使用してクエリを書き直して [`in()`](../query/inoperator.md) ください。

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
