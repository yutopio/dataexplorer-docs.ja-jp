---
title: ソースに追加されたデータに対する kusto 更新ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの更新ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 166d5f4f4d81957c49fb3fdedd3b2654985648ab
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97514067"
---
# <a name="update-policy-overview"></a>更新ポリシーの概要

[更新ポリシー](update-policy.md)では、ソーステーブルに挿入されたデータに対して実行される変換クエリに基づいて、ソーステーブルに新しいデータが挿入されるたびに、kusto がターゲットテーブルにデータを自動的に追加するように指示します。

:::image type="content" source="images/updatepolicy/update-policy-overview.png" alt-text="Azure データエクスプローラーの更新ポリシーの概要":::

たとえば、ポリシーを使用すると、1つのテーブルを別のテーブルのフィルター処理されたビューとして作成できます。 新しいテーブルには、異なるスキーマや保持ポリシーなどを設定できます。 

更新ポリシーには、通常のインジェストと同じ制限とベストプラクティスが適用されます。 ポリシーはクラスターのサイズによってスケールアウトされ、ingestions が大規模な組み込みで実行される場合はより効率的に動作します。

> [!NOTE]
> 更新ポリシーが定義されているソーステーブルとテーブルは、同じデータベース内に存在する必要があります。
> 更新ポリシー関数スキーマとターゲットテーブルスキーマは、列名、型、および順序と一致している必要があります。

## <a name="update-policys-query"></a>ポリシーのクエリの更新

更新ポリシークエリは、特別なモードで実行されます。このモードでは、新しく取り込まれたレコードのみが対象となり、このクエリの一部としてソーステーブルの取り込まれたデータに対してクエリを実行することはできません。 ただし、トランザクション更新ポリシーの "境界" 内のデータ取り込まれたは、1つのトランザクションでクエリで使用できるようになります。 更新ポリシーは変換先テーブルで定義されているので、データを1つのソーステーブルに取り込みと、そのデータに対して複数のクエリが実行される可能性があります。 複数の更新ポリシーの実行順序は定義されていません。 

### <a name="query-limitations"></a>クエリの制限事項 

* クエリでは、格納されている関数を呼び出すことができますが、複数データベースにまたがるクエリやクロスクラスタークエリを含めることはできません。 
* 更新ポリシーの一部として実行されるクエリには、 [RestrictedViewAccess ポリシー](restrictedviewaccesspolicy.md) が有効になっているか [行レベルセキュリティポリシー](rowlevelsecuritypolicy.md) が有効になっているテーブルに対する読み取りアクセス権がありません。
* `Source` `Query` ポリシーの一部またはパートによって参照される関数内のテーブルを参照する場合 `Query` :
   * テーブルの修飾名は使用しないでください。 代わりに `TableName` を使用してください。 
   * またはを使用しないで `database("DatabaseName").TableName` `cluster("ClusterName").database("DatabaseName").TableName` ください。
* ストリーミングインジェストの更新ポリシーの制限事項については、「 [ストリーミングインジェストの制限](../../ingest-data-streaming.md#limitations)」を参照してください。 

> [!WARNING]
> 更新ポリシーで正しくないクエリを定義すると、データがソーステーブルに取り込まれたされるのを防ぐことができます。

## <a name="the-update-policy-object"></a>更新ポリシーオブジェクト

テーブルには、0個、1個、または複数の更新ポリシーオブジェクトが関連付けられている場合があります。
これらの各オブジェクトは、次のプロパティが定義された JSON プロパティバッグとして表されます。

|プロパティ |種類 |説明  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |更新ポリシーが有効 (true) か無効 (false) かを示します。                                                                                                                               |
|source                        |`string`|更新ポリシーを起動するテーブルの名前                                                                                                                                 |
|クエリ                         |`string`|更新プログラムのデータを生成するために使用される Kusto CSL クエリ                                                                                                                           |
|IsTransactional               |`bool`  |更新ポリシーがトランザクションであるかどうかを示します (既定値は false)。 トランザクション更新ポリシーを実行できませんでした。ソーステーブルが新しいデータで更新されていません   |
|PropagateIngestionProperties  |`bool`  |ソーステーブルへの取り込み中に指定されたインジェストプロパティ (エクステントタグと作成時刻) も、派生テーブル内のものにも適用される必要があるかどうかを示します。                 |

> [!NOTE]
> 連鎖更新が許可されています (→→→ `TableA` `TableB` .. `TableC` .)。
>
> ただし、更新ポリシーが複数のテーブルに対して循環して定義されている場合、更新のチェーンは切り取られます。 この問題は、実行時に検出されます。 データは、影響を受けるテーブルのチェーン内の各テーブルに1回だけ取り込まれたされます。

## <a name="update-policy-commands"></a>ポリシーの更新コマンド

更新ポリシーを制御するコマンドは次のとおりです。

* [`.show table *TableName* policy update`](update-policy.md#show-update-policy) テーブルの現在の更新ポリシーを表示します。
* [`.alter table *TableName* policy update`](update-policy.md#alter-update-policy) テーブルの現在の更新ポリシーを設定します。
* [`.alter-merge table *TableName* policy update`](update-policy.md#alter-merge-table-tablename-policy-update) テーブルの現在の更新ポリシーに追加します。
* [`.delete table *TableName* policy update`](update-policy.md#delete-table-tablename-policy-update) テーブルの現在の更新ポリシーに追加します。

## <a name="update-policy-is-initiated-following-ingestion"></a>更新ポリシーはインジェスト後に開始されます

更新ポリシーは、次のいずれかのコマンドを使用して、定義されたソーステーブルにデータが取り込まれたまたは移動したときに有効になります。

* [。インジェスト (プル)](../management/data-ingestion/ingest-from-storage.md)
* [。インジェスト (インライン)](../management/data-ingestion/ingest-inline.md)
* [. set |. append |. set-or-append |. set-or-replace](../management/data-ingestion/ingest-from-query.md)
  * 更新ポリシーがコマンドの一部として呼び出されると  `.set-or-replace` 、既定の動作では、派生テーブル内のデータはソーステーブルと同じ方法で置き換えられます。
* [.move extents](./move-extents.md)
* [.replace extents](./replace-extents.md)
  * `PropagateIngestionProperties`コマンドはインジェスト操作でのみ有効です。 更新ポリシーがまたはコマンドの一部としてトリガーされた場合 `.move extents` `.replace extents` 、このオプションによる影響はありません。

## <a name="regular-ingestion-using-update-policy"></a>更新ポリシーを使用した通常のインジェスト

更新ポリシーは、次の条件が満たされた場合に通常のインジェストと同様に動作します。

* ソーステーブルは、自由形式の列として書式設定された興味深いデータを含む高料金のトレーステーブルです。 
* 更新ポリシーが定義されているターゲットテーブルは、特定のトレース行のみを受け入れます。
* テーブルには、適切に構造化されたスキーマがあります。これは、 [parse 演算子](../query/parseoperator.md)によって作成された元のフリーテキストデータの変換です。

## <a name="zero-retention-on-source-table"></a>ソーステーブルのリテンション期間なし

場合によっては、データがターゲットテーブルへのステップ実行のストーンとしてのみソーステーブルに取り込まれたされ、生データをソーステーブルに保持する必要がないことがあります。 ソーステーブルの [保有ポリシー](retentionpolicy.md)で論理的な削除期間を0に設定し、更新ポリシーをトランザクションとして設定します。 この状況では、次のようになります。 

* ソースデータは、ソーステーブルからはクエリできません。 
* 取り込み操作の一部として、ソースデータが永続的なストレージに保存されることはありません。 
* 運用上のパフォーマンスが向上します。 
* バックグラウンドのクリーンアップ操作のためのインジェスト後のリソースが削減されます。 これらの操作は、ソーステーブルの [エクステント](../management/extents-overview.md) に対して行われます。

## <a name="performance-impact"></a>パフォーマンスへの影響

更新ポリシーは、Kusto クラスターのパフォーマンスに影響を与える可能性があります。 更新ポリシーは、ソーステーブルへのインジェストに影響します。 複数のデータエクステントのインジェストには、対象テーブルの数が乗算されます。 その `Query` ため、更新ポリシーの一部が正常に機能するように最適化されていることが重要です。 インジェスト操作に対する更新ポリシーのその他のパフォーマンスへの影響をテストすることができます。 その部分で使用するポリシーまたは関数を作成または変更する前に、特定のおよび既存のエクステントでポリシーを呼び出し `Query` ます。

### <a name="evaluate-resource-usage"></a>リソースの使用状況の評価

[`.show queries`](../management/queries.md)次のシナリオでは、を使用してリソースの使用状況 (CPU、メモリなど) を評価します。
* ソーステーブル名 ( `Source` 更新ポリシーのプロパティ) は `MySourceTable` です。
* `Query`更新ポリシーのプロパティは、という名前の関数を呼び出し `MyFunction()` ます。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```

## <a name="failures"></a>エラー

既定では、更新ポリシーの実行に失敗しても、ソーステーブルへのデータの取り込みには影響しません。 ただし、更新ポリシーが次のように定義されている場合、 `IsTransactional` ポリシーの実行に失敗すると、ソーステーブルへのデータのインジェストが強制的に失敗します。 場合によっては、ソーステーブルへのデータの取り込みは成功しますが、ターゲットテーブルへの取り込み中に更新ポリシーが失敗します。

ポリシーの更新中に発生したエラーは、 [ `.show ingestion failures` コマンド](../management/ingestionfailures.md)を使用して取得できます。
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

### <a name="treatment-of-failures"></a>エラーの処理

#### <a name="non-transactional-policy"></a>非トランザクションポリシー 

このエラーは、Kusto によって無視されます。 再試行は、データインジェストプロセスの所有者の責任です。  

#### <a name="transactional-policy"></a>トランザクションポリシー

更新をトリガーした元の取り込み操作も失敗します。 ソーステーブルとデータベースは、新しいデータでは変更されません。
インジェスト方法 `pull` (kusto のデータ管理サービスがインジェストプロセスに含まれる) の場合、次のロジックに従って、Kusto のデータ管理サービスによって調整されたインジェスト操作全体に対して自動的に再試行が行われます。
* 再試行は、 `DataImporterMaximumRetryPeriod` (既定値 = 2 日) から `DataImporterMaximumRetryAttempts` (既定値 = 10) までの間に実行されます。
* 上記の設定はいずれも、データ管理サービスの構成で変更できます。
* バックオフ期間は2分から開始され、指数関数的に増加します (2 > 4-> 8 > 16...~

それ以外の場合は、データ所有者の責任です。