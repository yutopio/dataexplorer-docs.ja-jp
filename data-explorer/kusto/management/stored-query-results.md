---
title: 保存されたクエリ結果 (プレビュー)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで格納されているクエリ結果を作成して使用する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
ms.date: 12/3/2020
ms.openlocfilehash: 352f82bdb11847574807f81bc63236127900ae94
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933861"
---
# <a name="stored-query-results-preview"></a>保存されたクエリ結果 (プレビュー)

重いクエリの結果を保存し、迅速に取得します。
次のシナリオでは、格納されているクエリの結果が役に立ちます。
* 結果のページ割り当てを実装します。 ストアドクエリの結果はクエリに基づいて作成され、最初のページにプレビューが表示されます。 後続の各ページでは、初期クエリを再実行することなく、事前計算された結果の次の部分が示されます。
* データ探索中にクエリ結果を一時的に保存します。

> [!NOTE]
> 格納されているクエリの結果はプレビュー段階であり、運用環境のシナリオでは使用しないでください。 

保存されたクエリの結果には、作成時から最大24時間アクセスできます。 セキュリティポリシーの更新 (たとえば、データベースアクセス、行レベルのセキュリティなど) は、格納されているクエリの結果に反映されません。 ユーザーのアクセス許可の失効が発生した場合は [、drop stored_query_results](#drop-stored_query_results) を使用します。 格納されているクエリ結果には、それを作成したのと同じプリンシパル id でしかアクセスできません。 

格納されているクエリ結果はテーブルのように動作しますが、レコードの順序は保持されません。 結果を改ページするには、クエリに一意の ID 列を含めることをお勧めします。 詳しくは、[例](#examples)をご覧ください。 クエリによって返される結果セットが複数ある場合、最初の結果セットだけが格納されます。 

格納されているクエリ結果を使用するに `Database Viewer` は、以上のアクセスロールが必要です。

## <a name="store-the-results-of-a-query"></a>クエリの結果を格納する

**構文**

`.set``stored_query_result` *StoredQueryResultName* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <|*クエリ*

**引数**

* *StoredQueryResultName*: [エンティティ名](../query/schema-entities/entity-names.md) の規則に従った、保存されたクエリの結果名。
* *Query*: 結果が格納される、重い負荷の高い kql クエリ。
* *PropertyName*: (すべてのプロパティは省略可能です)
    
    | プロパティ       | Type       | 説明       |
    |----------------|------------|-------------------------------------------------------------------------------------|
    | `expiresAfter` | `timespan` | 格納されているクエリの結果の有効期限 (最大24時間) を示す timespan リテラル。 |
    | `previewCount` | `int`      | プレビューで返す行の数。 このプロパティを `0` (既定値) に設定すると、コマンドによってすべてのクエリ結果行が返されます。 |
    | `distributed`  | `bool`     | クエリを並列に実行しているすべてのノードからのクエリ結果が、コマンドによって格納されることを示します。 既定値は *true* です。 `distributed`フラグを *false* に設定すると、クエリによって生成されるデータの量が少ない場合や、多数のクラスターノードが大きい場合に、多数の小さなデータシャードが作成されないようにすることができます。 |

## <a name="retrieve-a-stored-query-result"></a>格納されているクエリ結果を取得する

格納されているクエリ結果を取得するには、 `stored_query_result()` クエリで関数を使用します。

`stored_query_result``(`' StoredQueryResultName ' `)` `|`...

## <a name="examples"></a>例

### <a name="simple-query"></a>単純なクエリ

単純なクエリ結果の格納:

<!-- csl -->
```kusto
.set stored_query_result Numbers <| range X from 1 to 1000000 step 1
```

**出力:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

格納されたクエリ結果の取得:

<!-- csl -->
```kusto
stored_query_result("Numbers")
```

**出力:**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

### <a name="pagination"></a>改ページ位置の自動修正

過去7日間に Ad ネットワークと日別にクリックを取得します。

<!-- csl -->
```kusto
.set stored_query_result DailyClicksByAdNetwork7Days with (previewCount = 100) <|
Events
| where Timestamp > ago(7d)
| where EventType == 'click'
| summarize Count=count() by Day=bin(Timestamp, 1d), AdNetwork
| order by Count desc
| project Num=row_number(), Day, AdNetwork, Count
```

**出力:**

| 番号 | 日間 | AdNetwork | Count |
|-----|-----|-----------|-------|
| 1 | 2020-01-01 00:00: 00.0000000 | NeoAds | 1002 |
| 2 | 2020-01-01 00:00: 00.0000000 | HighHorizons | 543 |
| 3 | 2020-01-01 00:00: 00.0000000 | PieAds | 379 |
| ... | ... | ... | ... |

次のページを取得します。

<!-- csl -->
```kusto
stored_query_result("DailyClicksByAdNetwork7Days")
| where Num between(100 .. 200)
```

**出力:**

| 番号 | 日間 | AdNetwork | Count |
|-----|-----|-----------|-------|
| 100 | 2020-01-01 00:00: 00.0000000 | CoolAds | 301 |
| 101 | 2020-01-01 00:00: 00.0000000 | DreamAds | 254 |
| 102 | 2020-01-02 00:00: 00.0000000 | SuperAds | 123 |
| ... | ... | ... | ... |

## <a name="control-commands"></a>管理コマンド

### <a name="show-stored_query_results"></a>。 stored_query_results を表示します

アクティブな格納済みクエリの結果に関する情報を表示します。

>[!NOTE]
> * または権限を持つユーザーは、 `DatabaseAdmin` `DatabaseMonitor` データベースのコンテキストで、アクティブなストアドクエリの結果が存在するかどうかを調べることができます。
> * または権限を持つユーザーは `DatabaseUser` `DatabaseViewer` 、プリンシパルによって作成された、アクティブなストアドクエリの結果が存在するかどうかを調べることができます。

#### <a name="syntax"></a>構文

`.show` `stored_query_results`

#### <a name="returns"></a>戻り値

| StoredQueryResultId | 名前 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | Event.manualintervention.createdon | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | events | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26: 49.6971487 | 2020-10-08 14:26: 49.6971487 |

### <a name="drop-stored_query_result"></a>。 drop stored_query_result

現在のプリンシパルによって現在のデータベースで作成された、アクティブなストアドクエリの結果を削除します。

#### <a name="syntax"></a>構文

`.drop``stored_query_result` *StoredQueryResultName*

`Database Viewer` このコマンドを呼び出すには、アクセス許可が必要です。

#### <a name="returns"></a>戻り値

削除された格納済みクエリの結果に関する情報を返します。次に例を示します。

| StoredQueryResultId | 名前 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | Event.manualintervention.createdon | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | events | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26: 49.6971487 | 2020-10-08 14:26: 49.6971487 |

### <a name="drop-stored_query_results"></a>。 drop stored_query_results

指定されたプリンシパルによって現在のデータベースで作成された、アクティブなストアドクエリの結果を削除します。

`Database Admin` このコマンドを呼び出すには、アクセス許可が必要です。

#### <a name="syntax"></a>構文

`.drop``stored_query_results` `created-by` *PrincipalName*

#### <a name="returns"></a>戻り値

削除された格納済みクエリの結果に関する情報を返します。

例:

<!-- csl -->
```kusto
.drop stored_query_results by user 'aadapp=c28e9b80-2808-bed525fc0fbb'
```

| StoredQueryResultId | 名前 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | Event.manualintervention.createdon | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | events | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26: 49.6971487 | 2020-10-08 14:26: 49.6971487 |
| 571f1a76f5a9199d4-b339 ba7caac19b46 | トレース | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 5212 | 100000 | 2020-10-07 14:31: 01.8271231| 2020-10-08 14:31: 01.8271231 |
