---
title: データのパーティション分割ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータのパーティション分割ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: c3f7212b062adaae1bd56399753270653204ad22
ms.sourcegitcommit: 53a727fceaa89e6022bc593a4aae70f1e0232f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652100"
---
# <a name="data-partitioning-policy"></a>データのパーティション分割ポリシー

パーティション分割ポリシーでは、特定のテーブルに対して [エクステント (データシャード)](../management/extents-overview.md) をどのようにパーティション分割するかを定義します。

ポリシーの主な目的は、パーティション分割された列でフィルター処理することによってデータセットを絞り込むことがわかっているクエリのパフォーマンスを向上させること、または高カーディナリティ文字列列に対して集計/結合を行うことです。 ポリシーによって、データの圧縮が向上する場合もあります。

> [!CAUTION]
> ポリシーを定義できるテーブルの数には、ハードコーディングされた制限はありません。 ただし、追加のテーブルごとに、クラスターのノードで実行されるバックグラウンドデータパーティション処理のオーバーヘッドが増加します。 これにより、より多くのクラスターリソースが使用される可能性があります。 詳細については、「 [監視](#monitoring) と [容量](#capacity)」を参照してください。

## <a name="partition-keys"></a>パーティション キー

次の種類のパーティションキーがサポートされています。

|種類                                                   |列の型 |パーティションのプロパティ                    |パーティションの値                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[ハッシュ](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範囲](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>ハッシュパーティションキー

> [!NOTE]
> 次のインスタンスでのみ、テーブルの型の列にハッシュパーティションキーを適用し `string` ます。
> * クエリの大部分で、等値フィルター (,) を使用する場合 `==` `in()` 。
> * クエリの大部分は `string` 、、、などの *大規模なディメンション* (10 mb 以上のカーディナリティ) の特定の型指定された列に対して集計/結合を `application_ID` `tenant_ID` `user_ID` 行います。

* ハッシュ剰余関数は、データをパーティション分割するために使用されます。
* 同種 (パーティション分割) エクステントのデータは、ハッシュパーティションキーによって並べ替えられます。
  * [行順序ポリシー](roworderpolicy.md)にハッシュパーティションキーを含める必要はありません (テーブルで定義されている場合)。
* [シャッフル戦略](../query/shufflequery.md)を使用し、 `shuffle key` で使用される、またはテーブルのハッシュパーティションキーとして使用されるクエリは、 `join` `summarize` `make-series` クラスターノード間での移動に必要なデータ量が大幅に減少するため、パフォーマンスが向上すると予測されます。

#### <a name="partition-properties"></a>パーティションのプロパティ

* `Function` 使用するハッシュ剰余関数の名前を指定します。
  * サポートされている値: `XxHash64` 。
* `MaxPartitionCount` 期間ごとに作成するパーティションの最大数 (ハッシュ剰余関数の剰余引数) です。
  * サポートされている値は、の範囲内 `(1,2048]` です。
    * 値は次のようになる必要があります。
      * クラスター内のノード数の5倍を超えています。
      * 列のカーディナリティより小さい。
    * 値が大きいほど、クラスターのノードでのデータパーティション分割プロセスのオーバーヘッドが大きくなり、各期間のエクステント数が大きくなります。
    * ノードが50未満のクラスターの場合は、の値から始めることをお勧め `256` します。
      * 必要に応じて、上記の考慮事項 (クラスター内のノードの数が増加するなど) に基づいて値を調整するか、クエリパフォーマンスの利点と、データの取り込み後のパーティション分割のオーバーヘッドに基づいて、値を調整します。
* `Seed` ハッシュ値のランダム化するに使用する値を指定します。
  * 値は正の整数である必要があります。
  * 推奨値はです `1` 。これは、指定されていない場合の既定値です。
* `PartitionAssignmentMode` は、クラスター内のノードにパーティションを割り当てるために使用されるモードです。
  * サポートされているモード:
    * `Default`: 同じパーティションに属するすべての同種 (パーティション分割) エクステントが同じノードに割り当てられます。
    * `Uniform`: エクステントのパーティション値は無視され、エクステントはクラスターのノードに均一に割り当てられます。
  * クエリがハッシュパーティションキーに対して結合または集計されない場合は、を使用 `Uniform` します。 それ以外の場合は、 `Default`を指定します。

#### <a name="example"></a>例

`string`という名前の型指定された列に対するハッシュパーティションキー `tenant_id` 。
この例では、ハッシュ関数を、の、および既定のを使用して使用して `XxHash64` `MaxPartitionCount` `256` `Seed` `1` います。

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1,
    "PartitionAssignmentMode": "Default"
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>Uniform range datetime パーティションキー

> [!NOTE] 
> テーブル `datetime` へのデータ取り込まれたがこの列に従って並べ替えられない場合は、テーブル内の型指定された列に対してのみ、uniform range datetime パーティションキーを適用します。

このような場合は、エクステント間でデータを再シャッフルして、各エクステントが一定期間のレコードを含むようにすると便利です。 これにより、その列のフィルターが `datetime` クエリ時により効率的になります。

使用されるパーティション関数は [bin_at ()](../query/binatfunction.md) であり、カスタマイズすることはできません。

#### <a name="partition-properties"></a>パーティションのプロパティ

* `RangeSize``timespan`各 datetime パーティションのサイズを示すスカラー定数を指定します。
  次のことをお勧めします。
  * 値 `1.00:00:00` (1 日) で開始します。
  * マージできない小さなエクステントがテーブルに多数含まれる可能性があるため、これより短い値を設定しないでください。
* `Reference` は `datetime` 、固定された時点を示すスカラー定数で、どの datetime パーティションがアラインされているかを示します。
  * から始めることをお勧め `1970-01-01 00:00:00` します。
  * Datetime パーティションキーに値があるレコードがある場合 `null` 、そのパーティション値はの値に設定され `Reference` ます。

#### <a name="example"></a>例

コードスニペットは、という名前の型指定された列に対する uniform datetime 範囲パーティションキーを示して `datetime` `timestamp` います。
を `datetime(1970-01-01)` 参照ポイントとして使用し、各パーティションにサイズを指定し `1d` ます。

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00"
  }
}
```

## <a name="the-policy-object"></a>ポリシーオブジェクト

既定では、テーブルのデータのパーティション分割ポリシーはです `null` 。この場合、テーブル内のデータはパーティション分割されません。

データのパーティション分割ポリシーには、次の主要なプロパティがあります。

* **Partitionkeys**:
  * テーブル内のデータをパーティション分割する方法を定義する [パーティションキー](#partition-keys) のコレクション。
  * テーブルは `2` 、次のいずれかのオプションを使用して、最大でキーをパーティション分割することができます。
    * 1つの [ハッシュパーティションキー](#hash-partition-key)。
    * 1つの [uniform range datetime パーティションキー](#uniform-range-datetime-partition-key)。
    * 1つの [ハッシュパーティションキー](#hash-partition-key) と1つの [uniform range datetime パーティションキー](#uniform-range-datetime-partition-key)。
  * 各パーティションキーには、次のプロパティがあります。
    * `ColumnName`: `string ` データのパーティション分割に使用される列の名前。
    * `Kind`: `string` -適用するデータパーティションの種類 ( `Hash` または `UniformRange` )。
    * `Properties`: `property bag` -パーティション分割の実行に応じてパラメーターを定義します。

* **EffectiveDateTime**:
  * ポリシーが有効になる UTC datetime。
  * このプロパティは省略可能です。 指定されていない場合は、ポリシーが適用された後、データ取り込まれたに対してポリシーが有効になります。
  * 保有期間によって削除される可能性のある同種でない (パーティション分割されていない) エクステントは、パーティション分割プロセスによって無視されます。これは、作成時間がテーブルの有効な論理的な削除期間の90% に先行するためです。

### <a name="example"></a>例

2つのパーティションキーを持つデータパーティション分割ポリシーオブジェクト。
1. `string`という名前の型指定された列に対するハッシュパーティションキー `tenant_id` 。
    - この例では、 `XxHash64` が256で、既定値がであるハッシュ関数が使用されて `MaxPartitionCount` `Seed` `1` います。
1. `datetime`という名前の型列に対する uniform datetime 範囲パーティションキー `timestamp` 。
    - を `datetime(1970-01-01)` 参照ポイントとして使用し、各パーティションにサイズを指定し `1d` ます。

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1,
        "PartitionAssignmentMode": "Default"
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00"
      }
    }
  ]
}
```

### <a name="additional-properties"></a>追加のプロパティ

次のプロパティは、ポリシーの一部として定義できますが、省略可能であり、変更しないことをお勧めします。

* **MinRowCountPerOperation**:
  * 1つのデータパーティション分割操作のソースエクステントの行数の合計の最小ターゲット。
  * このプロパティは省略可能です。 既定値は `0` です。

* **MaxRowCountPerOperation**:
  * 1つのデータパーティション分割操作のソースエクステントの行数の合計の最大ターゲット。
  * このプロパティは省略可能です。 既定値は `0` で、既定のターゲットは500万レコードです。
    * パーティション分割操作が操作ごとに非常に大量のメモリまたは CPU を消費している場合は、5分よりも小さい値を設定できます。 詳細については、「 [Monitoring](#monitoring)」を参照してください。

## <a name="notes"></a>Notes

### <a name="the-data-partitioning-process"></a>データパーティション分割プロセス

* データのパーティション分割は、クラスター内のインジェスト後のバックグラウンドプロセスとして実行されます。
  * に継続的に取り込まれたされるテーブルには、まだパーティション分割されていないデータの "後部" が常に存在している必要があります (非同種エクステント)。
* データのパーティション分割は、ポリシーのプロパティの値に関係なく、ホットエクステントでのみ実行さ `EffectiveDateTime` れます。
  * コールドエクステントをパーティション分割する必要がある場合は、 [キャッシュポリシー](cachepolicy.md)を一時的に調整する必要があります。

#### <a name="monitoring"></a>監視

クラスター内のパーティション分割の進行状況または状態を監視するには、 [. show diagnostics](../management/diagnostics.md#show-diagnostics) コマンドを使用します。

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

出力には次のものが含まれます。

  * `MinPartitioningPercentageInSingleTable`: クラスター内にデータのパーティション分割ポリシーがあるすべてのテーブルにわたる、パーティション分割されたデータの最小割合。
    * この割合が常に90% 未満の場合は、クラスターのパーティション分割 [容量](partitioningpolicy.md#capacity)を評価します。
  * `TableWithMinPartitioningPercentage`: パーティションの割合が上に表示されるテーブルの完全修飾名。

を使用 [します。](commands.md) パーティション分割コマンドとそのリソース使用率を監視するには、コマンドを表示します。 次に例を示します。

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

#### <a name="capacity"></a>容量

* データのパーティション分割プロセスにより、より多くのエクステントが作成されます。 クラスターによって、エクステントの [マージ容量](../management/capacitypolicy.md#extents-merge-capacity)が徐々に増加し、 [エクステントをマージ](../management/extents-overview.md) するプロセスが維持される可能性があります。
* 大量のインジェストが発生した場合、またはパーティション分割ポリシーが定義されているテーブルの数が多い場合、クラスターはエクステントの [パーティション容量](../management/capacitypolicy.md#extents-partition-capacity)を徐々に増やし、 [エクステントをパーティション分割するプロセス](#the-data-partitioning-process) を継続できるようにします。
* リソースが過剰に消費されるのを防ぐために、これらの動的な増加は制限されています。 完全に使用されていれば、上限を超えて徐々に増加することが必要になる場合があります。
  * 容量を増やすと[、クラスターの](../../manage-cluster-vertical-scaling.md)リソースの使用が大幅に増加する場合は、 / 手動で、または自動スケールを有効にすることで、クラスターをスケール[アウト](../../manage-cluster-horizontal-scaling.md)することができます。

### <a name="outliers-in-partitioned-columns"></a>パーティション分割された列の外れ値

* たとえば、空の文字列、汎用値 (やなど) など、他のユーザーよりもはるかに多くの値がハッシュパーティションキーに含まれている場合、または、 `null` `N/A` データセット内でより一般的なエンティティ (など) を表している場合は、 `tenant_id` クラスターノード間でのデータの分散が均等に分散され、クエリのパフォーマンスが低下します。
* Uniform range datetime パーティションキーの値の大部分が、列の値の大部分から "遠く" の値 (たとえば、遠くの値の大部分から遠い値) を超えている場合、データパーティション分割プロセスのオーバーヘッドが増加し、クラスターが追跡する必要がある多数の小さなエクステントが発生する可能性があります。

どちらの場合も、データを "修正" するか、インジェストの前または後にデータ内の不要なレコードをフィルターで除外して、クラスターでのデータのパーティション分割のオーバーヘッドを軽減する必要があります。 たとえば、 [更新ポリシー](updatepolicy.md)を使用します。

## <a name="next-steps"></a>次のステップ

[パーティション分割ポリシー制御コマンド](../management/partitioning-policy.md)を使用して、テーブルのデータのパーティション分割ポリシーを管理します。
