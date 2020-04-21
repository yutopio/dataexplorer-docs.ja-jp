---
title: クエリの制限 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリの制限について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 437e9781b9e29db7496292ba6a416f6e0c769e8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523071"
---
# <a name="query-limits"></a>クエリの制限

Kusto は、大規模なデータ セットをホストし、関連するすべてのデータをメモリ内に保持してクエリを満たそうとするアドホック クエリ エンジンであるため、クエリがサービス リソースを制限なしで独占するリスクが存在します。 Kusto は、既定のクエリ制限の形式で多数の組み込み保護を提供します。

## <a name="limit-on-query-concurrency"></a>クエリの同時実行の制限

**クエリの同時実行性**は、クラスターが同時に実行する複数のクエリに課す制限です。
クエリの同時実行制限の既定値は、実行されている SKU クラスターによって異なり、次のように計算されます。 `Cores-Per-Node x 10` たとえば、各マシンに 16 個の仮想コアがある D14v2 SKU にセットアップされているクラスターの場合、既定のクエリ同時実行の制限は`16 cores x10 = 160`になります。
デフォルト値は、サポートチケットを作成することで変更できます。 将来的には、このコントロールもコントロール コマンドを介して公開されます。

## <a name="limit-on-result-set-size-result-truncation"></a>結果セットのサイズの制限 (結果の切り捨て)

**結果の切り捨て**は、クエリによって返される結果セットに既定で設定される制限です。 Kusto は、クライアントに返されるレコードの数を**500,000**に制限し、それらのレコードのメモリ全体を**64 MB**に制限します。 これらの制限のいずれかを超えると、クエリは"部分クエリ失敗" で失敗します。 全体のメモリを超えると、次のメッセージが例外として生成されます。

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

レコード数を超えると失敗し、次の例外が発生します。

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

このエラーに対処するための戦略は次のとおりです。

* クエリを変更して、対象のないデータを返さないため、結果セットのサイズを小さくします。 これは、一般に、最初の (失敗した) クエリが "幅" の場合に役立ちます (たとえば、不要なデータ列は投影されません)。
* クエリ後の処理 (集計など) をクエリ自体に移行することによって、結果セットのサイズを小さくします。 これは、クエリの出力が別の処理システムに送られ、追加の集計が実行されるシナリオで役立ちます。 
* クエリから[データ エクスポート](../management/data-export/index.md)の使用への切り替え :
   これは、サービスから大量のデータをエクスポートする場合に適しています。
* このクエリ制限を抑制するようにサービスに指示します。

クエリによって生成される結果セットのサイズを小さくする一般的な方法は次のとおりです。

* クエリ出力で、[集計演算子](../query/summarizeoperator.md)グループと集計を使用して、集計[関数](../query/any-aggfunction.md)を使用していくつかの列をサンプリングする可能性があります。
* take[演算子](../query/takeoperator.md)を使用してクエリ出力をサンプリングする。
* [部分文字列関数](../query/substringfunction.md)を使用して、幅広いフリーテキスト列をトリムする。
* [プロジェクト演算子](../query/projectoperator.md)を使用して、結果セットから任意の対象でない列を削除します。

要求オプションを使用して結果の切り捨て`notruncation`を無効にすることができます。 この場合、何らかの制限が残っていることを強くお勧めします。 次に例を示します。

```kusto
set notruncation;
MyTable | take 1000000
```

(最大データ サイズ (バイト単位、デフォルトは 64 MB) と`truncationmaxsize``truncationmaxrecords`(レコードの最大数は 500,000) の値を設定することで、結果の切り捨てをより細かく制御することもできます。 たとえば、次のクエリは、1,105 レコードまたは 1MB のいずれか超えた方に、結果の切り捨てが行われると設定します。

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

Kusto クライアント ライブラリは現在、この制限の存在を前提としています。 制限なしで制限を増やすことができますが、最終的には、現在構成不可能なクライアントの制限に達します。 考えられる回避策の 1 つは、REST API コントラクトに直接プログラムし、Kusto クエリ結果のストリーミング パーサーを実装することです。 この問題に遭遇したかどうかを Kusto チームに知らせて、ストリーミング クライアントに適切な優先順位を付けることができるようにします。

結果の切り捨ては、クライアントに返される結果ストリームだけでなく、既定で適用されます。また、既定では、Kusto クラスターがクロスクラスター クエリで別の Kusto クラスターに発行するサブクエリにも適用され、同様の効果が適用されます。

## <a name="limit-on-memory-per-iterator"></a>反復器あたりのメモリの制限

**結果セット反復器あたりの最大メモリ**は、Kusto が「暴走」クエリから保護するために使用するもう 1 つの制限です。 この制限 (要求オプション`maxmemoryconsumptionperiterator`で表される) は、単一のクエリ プランの結果セット反復器が保持できるメモリの量の上限を設定します。 (この制限は、ストリーミングが本質的に行われなかった特定の反復子 (など`join`) に適用されます。この場合に返されるエラー メッセージをいくつか次に示します。

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

デフォルトでは、この値は 5 GB に設定されています。 この値を、マシンの物理メモリの半分まで増やす場合があります。

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

これらの制限を削除することを検討する場合は、まず実際に値を得るかどうかを判断します。 特に、結果の切り捨て限界を削除することは、エクスポートの目的 (その場合は`.export`コマンドを代わりに使用するよう促される) か、後で集計を行う (その場合は Kusto を使用して集計を検討する) ために、Bulk データを Kusto から移動することを意味します。
これらの提案されたソリューションのいずれでも満たされないビジネス シナリオがあるかどうかを Kusto チームに知らせてください。  

多くの場合、この制限を超えると、データ・セットを 10% にサンプリングすることが回避できます。 以下の 2 つのクエリは、このサンプリングの実行方法を示しています。 1 つ目は、統計サンプリングです (乱数ジェネレーターを使用)。 2 つ目は確定的サンプリングです (データ セットから列をハッシュすることで、通常は ID を使用します)。

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>ノードあたりのメモリの制限

**ノードごとのクエリあたりの最大メモリ**は、Kusto が "暴走" クエリから保護するために使用するもう 1 つの制限です。 この制限 (要求オプション`max_memory_consumption_per_query_per_node`で表される) は、特定のクエリに対して単一ノードに割り当てることができるメモリの量の上限を設定します。 


```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>累積文字列セットの制限

さまざまなクエリ操作では、Kusto は結果を生成する前に、文字列値を "収集" して内部でバッファリングする必要があります。 これらの累積文字列セットは、サイズと保持できるアイテム数に制限があります。 また、個々の文字列が一定の制限を超えることはできません。
これらの制限のいずれかを超えると、次のいずれかのエラーが発生します。

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

現在、最大文字列セットサイズを増やすスイッチはありません。
回避策として、クエリを再入力して、バッファリングする必要があるデータの量を減らします。 たとえば、不要な列を、join や集計などの演算子を「入力」する前に投影します。 または、たとえば、[シャッフル クエリ](../query/shufflequery.md)戦略を使用して。

## <a name="limit-on-request-execution-time-timeout"></a>要求実行時間の制限 (タイムアウト)

**サーバー タイムアウト**は、すべての要求に適用されるサービス側のタイムアウトです。
実行中の要求 (クエリおよび制御コマンド) のタイムアウトは、次の複数のポイントで実施されます。

* Kusto クライアント ライブラリ (使用する場合)
* 要求を受け入れる Kusto サービス エンドポイント内
* 要求を処理する Kusto サービス エンジンで

デフォルトでは、タイムアウトはクエリの場合は 4 分、制御コマンドでは 10 分に設定されます。 この値は、必要に応じて増やすことができます(1時間に制限されます)。

* Kusto.Explorer を使用してクエリを実行する場合は、[**ツール**&gt;**オプション接続*** &gt;**クエリ**&gt;サーバー**タイムアウト**] を使用します。
* プログラムを使用して`servertimeout`、クライアント要求プロパティ (型`System.TimeSpan`の値を 1 時間まで) 設定します。

タイムアウトに関する注意事項:

* クライアント側では、タイムアウトは、作成中の要求から、応答がクライアントに到着するまで適用されます。 クライアントでペイロードを読み取るのにかかる時間は、タイムアウトの一部として扱われないです (呼び出し元がストリームからデータを取得する速度によって異なります)。
* クライアント側でも、実際に使用されるタイムアウト値は、ユーザーが要求したサーバーのタイムアウト値よりも若干大きくなります。 これは、ネットワークの遅延を許容するためです。
* サービス側では、すべてのクエリ演算子がタイムアウト値を受け入れわけではありません。
   私たちは徐々にそのサポートを追加しています。
* Kusto が許可されている最大要求タイムアウトを自動的に使用するには、クライアント要求`norequesttimeout`プロパティ`true`を に設定します。

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here as it should not be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>クエリ CPU リソースの使用率の制限

既定では、Kusto は、実行しているクエリがクラスターの CPU リソースと同じ数の CPU リソースを使用することを許可し、複数のクエリが実行されている場合は、クエリ間で公平なラウンド ロビンを実行しようとします。 多くの場合、アドホック クエリのパフォーマンスが最適です。
他の場合は、特定のクエリに割り当てられている CPU リソースを制限する必要があります。 たとえば、"バックグラウンド ジョブ" を実行している場合、同時アドホック クエリを高い優先順位にするため、より高い待機時間を許容できます。

Kusto は、クエリを実行するときに **、query_fanout_threads_percent**と**query_fanout_nodes_percent**の 2 つの[クライアント要求プロパティ](../api/netfx/request-properties.md)の指定をサポートします。
どちらも、最大値 (100) に既定値を設定する整数ですが、特定のクエリの場合は、ある値に減らされる可能性があります。 最初の例では、スレッドの使用に対するファンアウト係数を制御します。100% の場合、クラスターは、各ノード (Azure D14 ノードにデプロイされたクラスター、16 個の CPU など) のすべての CPU をクエリに割り当てます。2 つ目の例は、サブクエリのディストリビューション操作ごとに使用するクラスター内のノード数を制御し、同様の方法で関数を実行します。

## <a name="limit-on-query-complexity"></a>クエリの複雑さの制限

クエリの実行中に、クエリ テキストはクエリを表す関係演算子のツリーに変換されます。
ツリーの深さが内部しきい値 (数千レベル) を超える場合、クエリは複雑すぎて処理できないと見なされ、関係演算子ツリーが制限を超えていることを示すエラー コードで失敗します。
ほとんどの場合、これは、次の例に示す、連結された二項演算子の長いリストを含むクエリが原因です。

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

この特定のケースでは、演算子を使用して[`in()`](../query/inoperator.md)クエリを書き直すことをお勧めします。 

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```