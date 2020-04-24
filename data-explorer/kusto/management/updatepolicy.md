---
title: ポリシーの更新 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの更新ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 812a5af932f69d898bb419631fa6ea3c6bc0795e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519399"
---
# <a name="update-policy"></a>Update ポリシー

更新ポリシーは、ソース**テーブル**に新しいデータが挿入されるたびに自動的にデータを追加するように Kusto に指示するターゲット**テーブル**に設定されます。 更新ポリシーの**クエリ**は、ソース テーブルに挿入されたデータに対して実行されます。 これにより、たとえば、別のテーブルのフィルタビューとして、別のスキーマ、保持ポリシーなどを使用してテーブルを作成できます。

既定では、更新ポリシーの実行に失敗しても、ソース テーブルへのデータの取り込みには影響しません。 更新ポリシーが**トランザクション**として定義されている場合、更新ポリシーの実行に失敗すると、ソース テーブルへのデータの取り込みも失敗します。 (更新ポリシーで不正なクエリを定義するなど、ユーザー エラーが発生した場合 **、ソース**テーブルにデータが取り込まれるのを防ぐことができるため、この操作を行う場合は注意が必要です)。トランザクション更新ポリシーの 「境界」で取り込まれたデータは、単一のトランザクションでクエリに使用できるようになります。

更新ポリシーのクエリは特別なモードで実行され、ソース テーブルに対して新しく取り込まれたデータのみが "表示" されます。 このクエリの一部として、ソース テーブルの既に取り込まれているデータをクエリすることはできません。 すぐに行うと、2 次時間の摂取が行われます。

更新ポリシーは変換先テーブルで定義されているため、データを 1 つのソース テーブルに取り込む場合、そのデータに対して複数のクエリが実行される可能性があります。 更新ポリシーの実行順序は未定義です。

たとえば、ソース テーブルが、対象データが自由書式の列として書式設定された高レートのトレース テーブルであるとします。 また、ターゲット テーブル (更新ポリシーが定義されている) が、特定のトレース行のみを受け入れ、Kusto の[解析演算子](../query/parseoperator.md)を使用して元の自由テキスト データを変換する適切に構造化されたスキーマを使用するとします。

更新ポリシーは、通常の取り込みと同様に動作し、同じ制限とベスト プラクティスの対象となります。 たとえば、クラスターのサイズに合わせてスケールアウトし、大量の大量の取り込みを行う場合は、より効率的に動作します。

## <a name="commands-that-trigger-the-update-policy"></a>更新ポリシーをトリガーするコマンド

更新ポリシーは、次のいずれかのコマンドを使用して、データが取り込まれるか、テーブルに移動された場合 (エクステントが作成される) ときに有効になります。

* [.摂取(プル)](../management/data-ingestion/ingest-from-storage.md)
* [.摂取(インライン)](../management/data-ingestion/ingest-inline.md)
* [.set | .append | .set または append | .set-or-replace](../management/data-ingestion/ingest-from-query.md)
* [移動範囲](../management/extents-commands.md#move-extents)
* [.範囲の置換](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>更新ポリシー オブジェクト

テーブルには、0 個、1 つ以上の更新ポリシー オブジェクトが関連付けられている場合があります。
このような各オブジェクトは、次のプロパティが定義された JSON プロパティ バッグとして表されます。

|プロパティ |Type |説明  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |更新ポリシーが有効 (true) または無効 (false) の状態                                                                                                                               |
|source                        |`string`|呼び出される更新ポリシーをトリガーするテーブルの名前                                                                                                                                 |
|クエリ                         |`string`|更新のデータを生成するために使用される Kusto CSL クエリ                                                                                                                           |
|トランザクショントランザクション               |`bool`  |更新ポリシーがトランザクションポリシーかどうかを示します (デフォルトは false)。 トランザクション更新ポリシーを実行しないと、ソース テーブルも新しいデータで更新されません。   |
|伝達インジェスティションプロパティ  |`bool`  |ソース テーブルへの取り込み中に指定されたインジェスト プロパティ (エクステント タグと作成時間) が、派生テーブルのプロパティにも適用される必要があるかどうかを示します。                 |

> [!NOTE]
>
> * 更新ポリシーが定義されているソース テーブルとテーブルは **、同じデータベースに含める必要があります**。
> * クエリにクロスデータベース クエリやクラスタ間クエリを含**む場合**があります。
> * クエリは、ストアドファンクションを呼び出すことができます。
> * クエリは、新しく取り込まれたレコードのみを対象に自動的にスコープされます。
> * カスケード更新が許可されます (TableA`[update]`-- -->`[update]`表B --`[update]`--> 表 - --> ..
> * 更新ポリシーが循環型で複数のテーブルに対して定義されている場合、これは実行時に検出され、更新の連鎖が切り取られます (つまり、データは影響を受けるテーブルのチェーン内の各テーブルに 1 回だけ取り込まれます)。
> * ポリシーの`Source``Query`一部 (または後者が参照する関数) でテーブルを参照する場合**は**、テーブルの修飾名 (つまり、`TableName`**使用ではなく、** `database("DatabaseName").TableName` )`cluster("ClusterName").database("DatabaseName").TableName`を使用しないようにしてください。
> * 更新ポリシーのクエリは、[有効になっている行レベル セキュリティ ポリシー](./rowlevelsecuritypolicy.md)を持つテーブルを参照できません。
> * 更新ポリシーの一部として実行されるクエリには[、RestrictedViewAccess ポリシー](restrictedviewaccesspolicy.md)が有効になっているテーブルへの読み取りアクセス権**がありません**。
> * `PropagateIngestionProperties`インジェクション操作でのみ有効になります。 更新ポリシーが`.move extents`or`.replace extents`コマンドの一部としてトリガーされる場合、このオプションは無効**です**。
> * 更新ポリシーが`.set-or-replace`コマンドの一部として呼び出されるとき、デフォルトの動作では、ソーステーブルにあるように、派生テーブルのデータも置き換えられます。

## <a name="retention-policy-on-the-source-table"></a>ソース テーブルの保持ポリシー

ソース テーブルの生データを保持しないように、更新ポリシーをトランザクションとして設定しながら、ソース テーブルの[保持ポリシー](retentionpolicy.md)に 0 のソフト削除期間を設定できます。

この結果、次のようになります。
* ソース データがソース テーブルからクエリ可能ではありません。
* 取得操作の一環として、ソース データが永続的な記憶域に保持されていません。
* 操作のパフォーマンスの向上。
* ソース テーブルの[エクステント](../management/extents-overview.md)で実行されたバックグラウンド グルーミング操作に、取り込み後に使用されるリソースの削減。

## <a name="failures"></a>エラー

場合によっては、ソース テーブルへのデータの取り込みが成功しますが、ターゲット テーブルへの取り込み中に更新ポリシーが失敗します。

ポリシーの更新中に発生したエラーは、次のように[.show ingestion failures コマンド](../management/ingestionfailures.md)を使用して取得できます。
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

失敗は次のように扱われます。

* **非トランザクション ポリシー**: このエラーは Kusto によって無視されます。 再試行は、データ所有者の責任です。  
* **トランザクション ポリシー**: 更新をトリガーした元のインジェス操作も失敗します。 ソース テーブルとデータベースは、新しいデータで変更されません。
  * インジェストメソッドが`pull`(Kusto のデータ管理サービスがインジェスト プロセスに関与している) 場合、次のロジックに従って Kusto のデータ管理サービスによって調整された、インジェスト操作全体に対して自動再試行が行われます。
    * 再試行は、最も早い (デフォルト`DataImporterMaximumRetryPeriod`= 2 日)`DataImporterMaximumRetryAttempts`と (デフォルト = 10) の間に到達するまで行われます。
    * 上記の設定はどちらも、KustoOps によってデータ管理サービスの構成で変更できます。
    * バックオフ期間は2分から始まり、指数関数的に成長します(2 -> 4 -> 8 -> 16..分)
  * それ以外の場合、再試行はデータ所有者の責任です。



## <a name="control-commands"></a>コントロール コマンド

* テーブルの現在の更新ポリシーを表示するには[、.show table ポリシーの更新](../management/update-policy.md#show-update-policy)を使用します。
* テーブルの現在の更新ポリシーを設定するには[、.alter テーブルのテーブル](../management/update-policy.md#alter-update-policy)ポリシーの更新を使用します。
* [テーブルの現在の更新ポリシーに追加するには、.alter-merge-table ポリシーの更新](../management/update-policy.md#alter-merge-table-table-policy-update)を使用します。
* [テーブルの現在の更新ポリシーに追加するには、.delete テーブルのテーブル](../management/update-policy.md#delete-table-table-policy-update)のポリシーの更新を使用します。

## <a name="testing-an-update-policys-performance-impact"></a>更新ポリシーのパフォーマンスへの影響のテスト

更新ポリシーを定義すると、ソース テーブルへのインジェストに影響するため、Kusto クラスターのパフォーマンスに影響を与える可能性があります。 更新ポリシーの一部を`Query`最適化して、パフォーマンスを向上することを強くお勧めします。
更新ポリシーの追加のパフォーマンスへの影響をテストするには、特定の既存のエクステントで呼び出してから、その`Query`部分で使用するポリシーや関数を作成または変更します。

次の例では以下の条件を前提としています。

* ソース テーブル名 (`Source`更新ポリシーのプロパティ) は`MySourceTable`です。
* 更新`Query`ポリシーのプロパティは、 という名前`MyFunction()`の関数を呼び出します。

[show クエリ](../management/queries.md)を使用すると、次のクエリのリソース使用率 (CPU、メモリなど) や、そのクエリの複数の実行を評価できます。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```