---
title: 具体化ビュー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの具体化されたビューについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 407db347d4d21450d5648fe8716e2d82553a9669
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320657"
---
# <a name="materialized-views-preview"></a>具体化ビュー (プレビュー)

[具体化](../../query/materialized-view-function.md) したビューでは、ソーステーブルに対する *集計* クエリが公開します。 具体化されたビューは常に、集計クエリ (常に最新の状態) の最新の結果を返します。 [具体化されたビューのクエリ](#materialized-views-queries) は、各クエリを実行するソーステーブルに対して直接集計を実行するよりもパフォーマンスが高くなります。

> [!NOTE]
> 具体化されたビューにはいくつかの [制限事項](materialized-view-create.md#limitations-on-creating-materialized-views)があり、すべてのシナリオで適切に動作するとは限りません。 この機能を使用する前に、 [パフォーマンスに関する考慮事項](#performance-considerations) を確認してください。

次のコマンドを使用して、具体化されたビューを管理します。
* [`.create materialized-view`](materialized-view-create.md)
* [`.alter materialized-view`](materialized-view-alter.md)
* [`.drop materialized-view`](materialized-view-drop.md)
* [`.disable | .enable materialized-view`](materialized-view-enable-disable.md)
* [`.show materialized-views commands`](materialized-view-show-commands.md)

## <a name="why-use-materialized-views"></a>具体化したビューを使用する理由

一般的に使用される集計の具体化されたビューのリソース (データストレージ、バックグラウンドの CPU サイクル) に投資することで、次の利点が得られます。

* **パフォーマンスの向上:** 具体化されたビューのクエリは、通常、同じ集計関数のソーステーブルに対してクエリを実行するよりも優れています。

* **鮮度:** 具体化されたビュークエリは、具体化が最後に行われたときとは無関係に、常に最新の結果を返します。 このクエリでは、ビューの具体化された部分とソーステーブル内のレコードが結合されていますが、これはまだ具体化されてい `delta` ません (部分)。これは常に最新の結果を提供します。

* **コストの削減:** [具体化されたビューのクエリを実行する](#materialized-views-queries) と、ソーステーブルに対して集計を行うよりも、クラスターのリソースの消費量が少なくなります。 集計のみが必要な場合は、ソーステーブルの保持ポリシーを減らすことができます。 この設定により、ソーステーブルのホットキャッシュコストが削減されます。

## <a name="materialized-views-use-cases"></a>具体化ビューのユースケース

具体化されたビューを使用して対処できる一般的なシナリオを次に示します。

* [ `arg_max()` (集計関数)](../../query/arg-max-aggfunction.md)を使用してエンティティごとに最後のレコードを照会します。
* [ `any()` (集計関数)](../../query/any-aggfunction.md)を使用してテーブル内のレコードを重複除去します。
* 生データに対して定期的な統計を計算することにより、データの解決を軽減します。 期間によって、さまざまな [集計関数](materialized-view-create.md#supported-aggregation-functions) を使用します。
    * たとえば、を使用して、1 `T | summarize dcount(User) by bin(Timestamp, 1d)` 日に個別のユーザーの最新のスナップショットを保持します。

すべてのユースケースの例については、「 [具体化ビューの作成コマンド](materialized-view-create.md#examples)」を参照してください。

## <a name="how-materialized-views-work"></a>具体化ビューのしくみ

具体化されたビューは、次の2つのコンポーネントで構成されます。

* *具体化* された部分-既に処理されているソーステーブルの集計レコードを保持する Azure データエクスプローラーテーブル。  このテーブルは、集計のグループ化の組み合わせごとに、常に1つのレコードを保持します。
* *デルタ*-まだ処理されていない、ソーステーブル内の新しく取り込まれたレコード。

具体化されたビューに対してクエリを実行すると、具体化された部分がデルタ部分と結合され、集計クエリの最新の結果が得られます。 オフラインの具体化プロセスでは、 *デルタ* から具体化されたテーブルに新しいレコードが取り込みされ、既存のレコードが置き換えられます。 置換は、置換するレコードを保持するエクステントを再構築することによって行われます。 *デルタ* 内のレコードが *具体化* された部分のすべてのデータシャードと常に交差している場合、具体化された各サイクルには *具体化* されたパーツ全体を再構築する必要があり、インジェスト率が遅れている可能性があります。 この場合、ビューは異常な状態になり、 *デルタ* は常に増加します。
このような状況のトラブルシューティング方法については、「 [監視](#materialized-views-monitoring) 」セクションを参照してください。

## <a name="materialized-views-queries"></a>具体化ビュークエリ

具体化されたビューに対してクエリを実行する主な方法は、テーブル参照のクエリのような名前になります。 具体化されたビューに対してクエリを行うと、ビューの具体化された部分と、まだ具体化されていないソーステーブル内のレコードが結合されます。 具体化されたビューに対してクエリを実行すると、ソーステーブルに取り込まれたしたすべてのレコードに基づいて、常に最新の結果が返されます。 具体化されたビューパーツの詳細については、「具体化された [ビューのしくみ](#how-materialized-views-work)」を参照してください。 

ビューに対してクエリを実行するもう1つの方法は、 [ `materialized_view()` 関数](../../query/materialized-view-function.md)を使用することです。 このオプションは、ビューの具体化された部分のみのクエリをサポートし、ユーザーが許容できる最大待機時間を指定します。 このオプションは最新のレコードを返すことは保証されていませんが、常にビュー全体を照会するよりもパフォーマンスが高くなります。 この機能は、テレメトリダッシュボードなど、パフォーマンスのために鮮度を犠牲にする場合に便利です。

ビューはクラスター間またはデータベース間クエリに参加できますが、ワイルドカードの共用体や検索には含まれません。

### <a name="examples"></a>例

1. ビュー全体に対してクエリを実行します。 ソーステーブルの最新のレコードが含まれています。
    
    <!-- csl -->
    ```kusto
    ViewName
    ```

1. ビューの具体化された部分のみを、最後に具体化された時点に関係なくクエリします。 

    <!-- csl -->
    ```kusto
    materialized_view("ViewName")
    ```
  
## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

具体化されたビューの正常性に影響を与える可能性がある主なコントリビューターは次のとおりです。

* **クラスターリソース:** クラスターで実行されている他のプロセスと同様に、具体化されたビューはクラスターからリソース (CPU、メモリ) を消費します。 クラスターが過負荷になっている場合、具体化されたビューを追加すると、クラスターのパフォーマンスが低下する可能性があります。 クラスターの正常性 [メトリック](../../../using-metrics.md#cluster-metrics)を使用してクラスターの正常性を監視します。 最適化された[自動スケール](../../../manage-cluster-horizontal-scaling.md#optimized-autoscale)では、現在、自動スケールルールの一部として、具体化されたビューの正常性を考慮しません

* **具体化されるデータと重複しています:** 具体化中は、最後の具体化 (デルタ) が処理され、ビューに具体化された後に、すべての新しいレコードがソーステーブルに取り込まれたます。 新規レコードと既に具体化されたレコードとの交差が大きいほど、具体化されたビューのパフォーマンスが低下します。 具体化されたビューは、更新されるレコード (ビューなど) の数 `arg_max` がソーステーブルの小さなサブセットである場合に最適に機能します。 具体化されたすべてのビューレコードを具体化サイクルごとに更新する必要がある場合、具体化されたビューは正常に動作しません。 [エクステントの再構築メトリック](../../../using-metrics.md#materialized-view-metrics)を使用して、この状況を特定します。

* **インジェスト率:** 具体化されたビューのソーステーブルでは、データボリュームまたはインジェストレートにハードコーディングされた制限はありません。 ただし、具体化されたビューで推奨されるインジェスト率は、1 ~ 2 GB/秒です。インジェスト率が高い場合でも十分に対応できます。 パフォーマンスは、クラスターのサイズ、使用可能なリソース、および既存のデータとの交差の量によって異なります。

* **クラスター内の具体化されるビューの数:** 上記の考慮事項は、クラスターで定義されている個々の具体化されたビューに適用されます。 各ビューは独自のリソースを消費し、多くのビューは使用可能なリソースで相互に競合します。 クラスター内の具体化されたビューの数には、ハードコーディングされた制限はありません。 ただし、一般的な推奨事項は、クラスター上の具体化されたビューの数が10個以下であることです。 クラスターで1つの具体化されたビューが定義されている場合は、 [容量ポリシー](../capacitypolicy.md#materialized-views-capacity-policy) を調整できます。

* **具体化** されたビューの定義: 具体化されたビューの定義は、最適なクエリパフォーマンスを得るためのクエリのベストプラクティスに従って定義する必要があります。 詳細については、「 [create command performance tips](materialized-view-create.md#performance-tips)」を参照してください。

## <a name="materialized-views-policies"></a>具体化したビューのポリシー

具体化されたビューの [保持ポリシー](../retentionpolicy.md) と [キャッシュポリシー](../cachepolicy.md) は、Azure データエクスプローラーテーブルと同様に定義できます。

具体化されたビューは、既定でデータベースの保有期間とキャッシュポリシーを取得します。 ポリシーは、 [保持ポリシーの制御コマンド](../retention-policy.md) または [キャッシュポリシーの制御コマンド](../cache-policy.md)を使用して変更できます。
   
   * 具体化されたビューの保持ポリシーは、ソーステーブルの保持ポリシーとは関係がありません。
   * ソーステーブルのレコードが使用されていない場合は、ソーステーブルの保持ポリシーを最小値に削除できます。 具体化されたビューは、ビューに設定されている保持ポリシーに従ってデータを保存します。 
   * 具体化されたビューはプレビューモードですが、少なくとも7日以上、回復性が true に設定されていることをお勧めします。 この設定により、エラーや診断のための迅速な回復が可能になります。
    
> [!NOTE]
> ソーステーブルに対する0の保持ポリシーは、現在サポートされていません。

## <a name="materialized-views-monitoring"></a>具体化ビューの監視

次の方法で、具体化されたビューの正常性を監視します。

* Azure portal の具体化された [ビューメトリック](../../../using-metrics.md#materialized-view-metrics) を監視します。
* `IsHealthy`から返されたプロパティを監視 [`.show materialized-view`](materialized-view-show-commands.md#show-materialized-view) します。
* を使用してエラーを確認 [`.show materialized-view failures`](materialized-view-show-commands.md#show-materialized-view-failures) します。

> [!NOTE]
> 具体化は、一定のエラーがある場合でも、データをスキップしません。 ビューは常に、ソーステーブル内のすべてのレコードに基づいて、クエリの最新のスナップショットを返すことが保証されます。 定数エラーによってクエリのパフォーマンスが大幅に低下しますが、ビュークエリで結果が不正確になることはありません。

### <a name="track-resource-consumption"></a>リソース消費量の追跡

具体化された **ビューのリソース消費:** 具体化されたビューの具体化プロセスによって消費されるリソースは、コマンドを使用して追跡できます [`.show commands-and-queries`](../commands-and-queries.md#show-commands-and-queries) 。 次のものを使用して、特定のビューのレコードをフィルター処理します (との置換 `DatabaseName` `ViewName` ):

<!-- csl -->
```
.show commands-and-queries 
| where Database  == "DatabaseName" and ClientActivityId startswith "DN.MaterializedViews;ViewName;"
```

### <a name="troubleshooting-unhealthy-materialized-views"></a>問題のある具体化ビューのトラブルシューティング

メトリックは、 `MaterializedViewHealth` 具体化されたビューが正常であるかどうかを示します。 具体化されたビューは、次のいずれかまたはすべての理由で異常になることがあります。
* 具体化処理が失敗しています。
* クラスターには、すべての受信データを同時に具体化するのに十分な容量がありません。 実行中にエラーが発生することはありません。 ただし、ビューは遅れているしていて、インジェスト速度を維持することができないため、正常な状態ではありません。

具体化されたビューが異常になる前に、メトリックによって示される経過期間 `MaterializedViewAgeMinutes` が徐々に増加します。

### <a name="troubleshooting-examples"></a>トラブルシューティングの例

次の例は、問題のあるビューを診断して修正するのに役立ちます。

* **エラー:** ソーステーブルが変更または削除されたか、ビューがに設定されてい `autoUpdateSchema` ないか、またはソーステーブルの変更が自動更新でサポートされていません。 <br>
   **診断:** `MaterializedViewResult`メトリックが起動され、 `Result` ディメンションがに設定され `SourceTableSchemaChange` / `SourceTableNotFound` ます。

* **エラー:** クラスターリソースが不足しているため、具体化プロセスは失敗し、クエリの制限に達します。 <br>
  **診断:** `MaterializedViewResult` メトリック `Result` ディメンションがに設定されて `InsufficientResources` います。 Azure データエクスプローラーはこの状態から自動的に回復しようとします。そのため、このエラーは一時的なものである可能性があります。 ただし、ビューが異常で、このエラーが常に生成される場合は、現在のクラスターの構成が取り込み速度を維持できず、クラスターのスケールアップまたはスケールアウトが必要になる可能性があります。

* **エラー:** 具体化プロセスは、他の (不明な) 理由により失敗しています。 <br> 
   **診断**: `MaterializedViewResult` メトリックの `Result` はになり `UnknownError` ます。 このエラーが頻繁に発生する場合は、Azure データエクスプローラーチームのサポートチケットを開いて、さらに調査してください。

具体化に失敗しない場合は `MaterializedViewResult` 、を使用して実行が成功するたびにメトリックが起動され `Result` = `Success` ます。 具体化されたビューは、遅れている (しきい値を超えている) 場合、正常に実行された場合には異常な状態になることがあり `Age` ます。 この状況は、次のような状況で発生する可能性があります。
   * 各具体化サイクルで再構築するエクステントが多すぎるため、具体化に時間がかかります。 エクステントの再構築がビューのパフォーマンスに与える影響の詳細については、「具体化された [ビューのしくみ](#how-materialized-views-work)」を参照してください。 
   * 各具体化サイクルがビュー内のエクステントの100% に近い状態で再構築する必要がある場合、ビューは正常に完了しない可能性があり、異常になります。 各サイクルで再構築されたエクステントの数は、メトリックに表示され `MaterializedViewExtentsRebuild` ます。 [具体化された [ビュー容量] ポリシー](../capacitypolicy.md#materialized-views-capacity-policy) で再構築されたエクステント数を増やすと、この場合にも役立つことがあります。 
   * クラスターに追加の具体化されたビューがあります。クラスターには、すべてのビューを実行するのに十分な容量がありません。 同時に実行される具体化されたビュー数の既定の設定を変更するには、「具体化された [ビュー容量ポリシー](../capacitypolicy.md#materialized-views-capacity-policy) 」を参照してください。

## <a name="next-steps"></a>次の手順

* [`.create materialized view`](materialized-view-create.md)
* [`.alter materialized-view`](materialized-view-alter.md)
* [具体化されるビューでのコマンドの表示](materialized-view-show-commands.md)
