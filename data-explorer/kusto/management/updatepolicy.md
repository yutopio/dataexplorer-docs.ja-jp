---
title: ソースに追加されたデータに対する kusto 更新ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの更新ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 7d2b89e0723bbecb29ffd582ae20c2ee41199e45
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618498"
---
# <a name="update-policy"></a>Update ポリシー

更新ポリシーは、**ソーステーブル**に新しいデータが挿入されるたびに、kusto に自動的にデータを追加するように指示する**ターゲットテーブル**に対して設定されます。 更新ポリシーの**クエリ**は、ソーステーブルに挿入されたデータに対して実行されます。 これにより、たとえば、別のテーブルのフィルター処理されたビューとして1つのテーブルを作成することができます。これは、場合によっては、異なるスキーマや保持ポリシーなどを使用します。

既定では、更新ポリシーの実行に失敗しても、ソーステーブルへのデータの取り込みには影響しません。 更新ポリシーが**トランザクション**として定義されている場合、更新ポリシーの実行に失敗すると、ソーステーブルへのデータの取り込みも強制的に失敗します。 (更新ポリシーでの不適切なクエリの定義など、一部のユーザーエラーによって **、データが**ソーステーブルに取り込まれたされない場合があるため、この操作を実行するときは注意が必要です。)トランザクション更新ポリシーの "境界" 内のデータ取り込まれたは、1つのトランザクションでクエリで使用できるようになります。

更新ポリシーのクエリは特殊なモードで実行されます。このモードでは、ソーステーブルに新しく取り込まれたデータのみが "表示" されます。 このクエリの一部として、ソーステーブルの取り込まれたデータに対してクエリを実行することはできません。 これにより、2次 ingestions の結果がすぐに得られます。

更新ポリシーは変換先テーブルで定義されているので、データを1つのソーステーブルに取り込みと、そのデータに対して複数のクエリが実行される可能性があります。 更新ポリシーの実行順序は定義されていません。

たとえば、ソーステーブルが、フリーテキスト列として書式設定された興味深いデータを含む高レートのトレーステーブルであるとします。 また、更新ポリシーが定義されているターゲットテーブルでは、特定のトレース行のみを受け入れ、適切に構造化されたスキーマを使用して、Kusto の[parse 演算子](../query/parseoperator.md)を使用して元のフリーテキストデータを変換するとします。

更新ポリシーは、通常のインジェストと同様に動作し、同じ制限事項とベストプラクティスが適用されます。 たとえば、クラスターのサイズを使用してスケールアウトし、ingestions が大きな形で実行される場合はより効率的に動作します。

## <a name="commands-that-trigger-the-update-policy"></a>更新ポリシーをトリガーするコマンド

更新ポリシーは、次のコマンドのいずれかを使用して、データが取り込まれたされた場合、またはテーブルに移動された場合 (エクステントが作成される場合) に有効になります。

* [。インジェスト (プル)](../management/data-ingestion/ingest-from-storage.md)
* [。インジェスト (インライン)](../management/data-ingestion/ingest-inline.md)
* [. set |. append |. set-or-append |. set-or-replace](../management/data-ingestion/ingest-from-query.md)
* [。エクステントを移動します。](../management/extents-commands.md#move-extents)
* [。エクステントの置換](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>更新ポリシーオブジェクト

テーブルには、0個、1個、または複数の更新ポリシーオブジェクトが関連付けられている場合があります。
これらの各オブジェクトは、次のプロパティが定義された JSON プロパティバッグとして表されます。

|プロパティ |Type |説明  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |更新ポリシーが有効 (true) か無効 (false) かを示します。                                                                                                                               |
|source                        |`string`|更新ポリシーを起動するテーブルの名前                                                                                                                                 |
|クエリ                         |`string`|更新プログラムのデータを生成するために使用される Kusto CSL クエリ                                                                                                                           |
|IsTransactional               |`bool`  |更新ポリシーがトランザクションであるかどうかを示します (既定値は false)。 トランザクション更新ポリシーの実行に失敗すると、ソーステーブルも新しいデータで更新されません。   |
|PropagateIngestionProperties  |`bool`  |ソーステーブルへの取り込み中に指定されたインジェストプロパティ (エクステントタグと作成時刻) が、派生テーブル内のものにも適用されるかどうかを示します。                 |

> [!NOTE]
>
> * 更新ポリシーが定義されているソーステーブルとテーブルは、**同じデータベース内に存在する必要があり**ます。
> * このクエリには、複数のデータベースにまたがるクエリやクロスクラスタークエリを含めることはでき**ません**。
> * クエリでは、格納されている関数を呼び出すことができます。
> * クエリのスコープは、新しく取り込まれたたレコードのみを対象とするように自動的に設定されます。
> * カスケード更新が許可されてい`[update]`ます (TableA----`[update]`> tableb----`[update]`> tablec---->...)
> * 更新ポリシーが複数のテーブルに循環的に定義されている場合は、実行時に検出され、更新プログラムのチェーンは切り取られます (つまり、影響を受けるテーブルのチェーン内の各テーブルに対してデータが1回だけ取り込まれたされます)。
> * `Source`ポリシーの`Query`一部 (または後者が参照する関数) でテーブルを参照するときは、テーブルの修飾名を使用し**ない**ようにしてください (つまり`TableName` 、とで`cluster("ClusterName").database("DatabaseName").TableName`は**なく** `database("DatabaseName").TableName`を使用します)。
> * 更新ポリシーのクエリでは、[行レベルセキュリティポリシー](./rowlevelsecuritypolicy.md)が有効になっているテーブルを参照することはできません。
> * 更新ポリシーの一部として実行されるクエリには、 [RestrictedViewAccess ポリシー](restrictedviewaccesspolicy.md)が有効になっているテーブルに対する読み取りアクセス権があり**ません**。
> * `PropagateIngestionProperties`インジェスト操作でのみ有効になります。 更新ポリシーが`.move extents`または`.replace extents`コマンドの一部としてトリガーされた場合、このオプションによる影響は**ありません**。
> * 更新ポリシーが`.set-or-replace`コマンドの一部として呼び出されると、既定の動作として、派生テーブル内のデータも、ソーステーブルの場合と同じように置換されます。

## <a name="retention-policy-on-the-source-table"></a>ソーステーブルのアイテム保持ポリシー

そのため、ソーステーブルに生データを保持しない場合は、更新ポリシーをトランザクションとして設定すると同時に、ソーステーブルの[保有ポリシー](retentionpolicy.md)で論理的な削除期間を0に設定できます。

これは次のようになります。
* ソースデータがソーステーブルからクエリ可能ではありません。
* 取り込み操作の一部として、持続性のあるストレージにソースデータが永続化されていません。
* 操作のパフォーマンスが向上しました。
* ソーステーブルの[エクステント](../management/extents-overview.md)に対して実行されるバックグラウンドのクリーンアップ操作について、インジェストを使用したリソースの削減。

## <a name="failures"></a>エラー

場合によっては、ソーステーブルへのデータの取り込みは成功しますが、ターゲットテーブルへの取り込み中に更新ポリシーが失敗します。

ポリシーの更新中に発生したエラーは、次のように、[[インジェストエラーの表示] コマンド](../management/ingestionfailures.md)を使用して取得できます。
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

エラーは次のように処理されます。

* **非トランザクションポリシー**: Kusto では、このエラーは無視されます。 再試行は、データ所有者の責任です。  
* **トランザクションポリシー**: 更新をトリガーした元の取り込み操作も失敗します。 ソーステーブルとデータベースは、新しいデータでは変更されません。
  * インジェスト方法がの場合 ( `pull` kusto のデータ管理サービスがインジェストプロセスに関係している場合)、次のロジックに従って、kusto のデータ管理サービスによって調整されたインジェスト操作全体に対して、自動的な再試行が行われます。
    * 再試行は、(既定値 = 2 `DataImporterMaximumRetryPeriod`日) `DataImporterMaximumRetryAttempts`から (既定値 = 10) までの間に到達するまで実行されます。
    * 上記の設定は、データ管理サービスの構成で KustoOps によって変更できます。
    * バックオフ期間は2分から始まり、指数関数的に増加します (2 > 4-> 8 > 16...~
  * それ以外の場合は、データ所有者の責任です。



## <a name="control-commands"></a>制御コマンド

* を使用[します。](../management/update-policy.md#show-update-policy)テーブルのテーブルポリシーの更新を表示して、テーブルの現在の更新ポリシーを表示します。
* テーブルの現在の更新ポリシーを設定するには、 [alter TABLE table policy update](../management/update-policy.md#alter-update-policy)を使用します。
* テーブルの現在の更新ポリシーに追加するには、 [alter-merge TABLE table policy update を使用します。](../management/update-policy.md#alter-merge-table-table-policy-update)
* を使用[します。](../management/update-policy.md#delete-table-table-policy-update)テーブルのテーブルポリシーの更新を削除して、テーブルの現在の更新ポリシーに追加します。

## <a name="testing-an-update-policys-performance-impact"></a>更新ポリシーのパフォーマンスへの影響のテスト

更新ポリシーを定義すると、Kusto クラスターのパフォーマンスに影響を与えることがあります。これは、ソーステーブルへの取り込みに影響するためです。 更新ポリシーの一部は、 `Query`正常に動作するように最適化することを強くお勧めします。
更新ポリシーの追加のパフォーマンスへの影響をテストするには、事前に特定のエクステントと既存のエクステントを呼び出して、ポリシーやその`Query`パーツで使用される関数を作成または変更する前に、既存の特定のエクステントに対して更新ポリシーを呼び出します。

次の例では以下の条件を前提としています。

* ソーステーブル名 (更新ポリシー `Source`のプロパティ) は`MySourceTable`です。
* 更新`Query`ポリシーのプロパティは、という名前`MyFunction()`の関数を呼び出します。

を使用[してクエリを表示](../management/queries.md)すると、次のクエリのリソース使用率 (CPU、メモリなど) を評価したり、複数回実行したりすることができます。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```