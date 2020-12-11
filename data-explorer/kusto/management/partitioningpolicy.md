---
title: データのパーティション分割ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータのパーティション分割ポリシーと、それを使用してクエリのパフォーマンスを向上させる方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: fdf72c8c58ec8a9fb9c64ecea6219dcc3a736d18
ms.sourcegitcommit: 2bdb904e6253c9ceb8f1eaa2da35fcf27e13a2cd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97091353"
---
# <a name="partitioning-policy"></a>Partitioning ポリシー

パーティション分割ポリシーでは、特定のテーブルに対して [エクステント (データシャード)](../management/extents-overview.md) をどのようにパーティション分割するかを定義します。

ポリシーの主な目的は、データセットを絞り込むクエリのパフォーマンスを向上させることです。たとえば、パーティション分割された列に対してフィルター処理を行うクエリ、集計を使用するクエリ、または高カーディナリティ文字列型の列で結合するクエリです。 ポリシーによって、データ圧縮が向上する場合もあります。

> [!CAUTION]
> パーティション分割ポリシーが定義されているテーブルの数には、ハードコーディングされた制限はありません。 ただし、追加のテーブルごとに、クラスターのノードで実行されるバックグラウンドデータパーティション処理のオーバーヘッドが増加します。 テーブルを追加すると、より多くのクラスターリソースが使用される可能性があります。 詳細については、「 [監視](#monitor-partitioning) と [容量](#partition-capacity)」を参照してください。

## <a name="partition-keys"></a>パーティション キー

次の種類のパーティションキーがサポートされています。

|種類                                                   |列の型 |パーティションのプロパティ                                               |パーティションの値                                        |
|-------------------------------------------------------|------------|-------------------------------------------------------------------|-------------------------------------------------------|
|[ハッシュ](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed`, `PartitionAssignmentMode` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範囲](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`, `OverrideCreationTime`                   | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>ハッシュパーティションキー

> [!NOTE]
> 次のインスタンスでのみ、テーブルの型の列にハッシュパーティションキーを適用し `string` ます。
> * クエリの大部分で、等値フィルター (,) を使用する場合 `==` `in()` 。
> * クエリの大部分は `string` 、、、などの *大規模なディメンション* (10 mb 以上のカーディナリティ) の特定の型指定された列に対して集計/結合を `application_ID` `tenant_ID` `user_ID` 行います。

* ハッシュ剰余関数は、データをパーティション分割するために使用されます。
* 同種 (パーティション分割) エクステントのデータは、ハッシュパーティションキーによって並べ替えられます。
  * [行順序ポリシー](roworderpolicy.md)にハッシュパーティションキーを含める必要はありません (テーブルで定義されている場合)。
* [シャッフル戦略](../query/shufflequery.md)を使用し、 `shuffle key` で使用される、またはテーブルのハッシュパーティションキーとして使用されるクエリは、 `join` `summarize` `make-series` クラスターノード間での移動に必要なデータ量が削減されるため、パフォーマンスが向上します。

#### <a name="partition-properties"></a>パーティションのプロパティ

|プロパティ | 説明 | サポートされる値| 推奨値 |
|---|---|---|---|
| `Function` | 使用するハッシュ剰余関数の名前。| `XxHash64` | |
| `MaxPartitionCount` | 作成するパーティションの最大数 (1 期間あたりのハッシュ剰余関数の剰余引数)。 | を指定 `(1,2048]` します。 <br>  クラスター内のノード数の5倍を超え、列のカーディナリティよりも小さくなります。 |  値を大きくすると、クラスターのノードでのデータパーティション分割プロセスのオーバーヘッドが増加し、各期間のエクステント数が増えます。 ノード数が50未満のクラスターの場合は、から開始 `256` します。 これらの考慮事項に基づいて値を調整するか、クエリパフォーマンスの利点と、データの取り込み後のパーティション分割のオーバーヘッドに基づいて値を調整します。
| `Seed` | ハッシュ値をランダム化するに使用します。 | 正の整数。 | `1`。これは既定値でもあります。 |
| `PartitionAssignmentMode` | クラスター内のノードにパーティションを割り当てるために使用されるモード。 | `Default`: 同じパーティションに属するすべての同種 (パーティション分割) エクステントが同じノードに割り当てられます。 <br> `Uniform`: エクステントのパーティション値は無視されます。 エクステントは、クラスターのノードに均一に割り当てられます。 | クエリがハッシュパーティションキーに対して結合または集計されない場合は、を使用 `Uniform` します。 それ以外の場合は、 `Default`を指定します。 |

#### <a name="hash-partition-key-example"></a>ハッシュパーティションキーの例

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

このような場合は、エクステント間でデータを再シャッフルして、各エクステントに一定期間内のレコードが含まれるようにすることができます。 この処理を行うと、 `datetime` クエリ時に列のフィルターがより効果的になります。

使用されるパーティション関数は [bin_at ()](../query/binatfunction.md) であり、カスタマイズすることはできません。

#### <a name="partition-properties"></a>パーティションのプロパティ

|プロパティ                | 説明                                                                                                                                                     | 推奨値                                                                                                                                                                                                                                                            |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `RangeSize`            | `timespan`各 datetime パーティションのサイズを示すスカラー定数です。                                                                                | 値 `1.00:00:00` (1 日) で開始します。 テーブルにはマージできない小さなエクステントが多数含まれる可能性があるため、より短い値を設定しないでください。                                                                                                      |
| `Reference`            | `datetime`どの datetime パーティションがアラインされるかに従って、固定された時点を示すスカラー定数。                                          | `1970-01-01 00:00:00`で開始します。 Datetime パーティションキーに値があるレコードがある場合 `null` 、そのパーティション値はの値に設定され `Reference` ます。                                                                                                      |
| `OverrideCreationTime` | `bool`結果エクステントの最小および最大作成時間が、パーティションキーの値の範囲によって上書きされるかどうかを示すです。 | 既定値は `false` です。 `true`データが到着時刻の順序に従っていない場合 (たとえば、1つのソースファイルには、遠くにある datetime 値が含まれている場合があります)、または、インジェストの時間ではなく、datetime 値に基づいて保持/キャッシュを強制する場合は、に設定します。 |

#### <a name="uniform-range-datetime-partition-example"></a>Uniform range datetime パーティションの例

このスニペットは、という名前の型指定された列に対する uniform datetime 範囲パーティションキーを示して `datetime` `timestamp` います。
は `datetime(1970-01-01)` 、各パーティションにのサイズを持つ参照ポイントとしてを使用 `1d` し、エクステントの作成時間をオーバーライドしません。

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00",
    "OverrideCreationTime": false
  }
}
```

## <a name="the-policy-object"></a>ポリシーオブジェクト

既定では、テーブルのデータのパーティション分割ポリシーはです `null` 。この場合、テーブル内のデータはパーティション分割されません。

データのパーティション分割ポリシーには、次の主要なプロパティがあります。

* **Partitionkeys**:
  * テーブル内のデータをパーティション分割する方法を定義する [パーティションキー](#partition-keys) のコレクション。
  * テーブルは `2` 、次のいずれかのオプションを使用して、最大でキーをパーティション分割できます。
    * 1つの [ハッシュパーティションキー](#hash-partition-key)。
    * 1つの [uniform range datetime パーティションキー](#uniform-range-datetime-partition-key)。
    * 1つの [ハッシュパーティションキー](#hash-partition-key) と1つの [uniform range datetime パーティションキー](#uniform-range-datetime-partition-key)。
  * 各パーティションキーには、次のプロパティがあります。
    * `ColumnName`: `string ` データのパーティション分割に使用される列の名前。
    * `Kind`: `string` -適用するデータパーティションの種類 ( `Hash` または `UniformRange` )。
    * `Properties`: `property bag` -パーティション分割の実行に応じてパラメーターを定義します。

* **EffectiveDateTime**:
  * ポリシーが有効になる UTC datetime。
  * このプロパティは省略可能です。 指定されていない場合、ポリシーは、ポリシーが適用された後、データ取り込まれたで有効になります。
  * 保有期間のために削除される可能性のある同種でない (パーティション分割されていない) エクステントは、パーティション分割プロセスによって無視されます。 これらのエクステントは、作成時間がテーブルの有効な論理的な削除期間の90% に先行するため、無視されます。
    > [!NOTE]
    > Datetime 値を設定して、取り込まれたデータをパーティション分割することができます。 ただし、この方法では、パーティション分割プロセスで使用されるリソースが大幅に増加する可能性があります。
    > ポリシーを変更するたびに、最大で数日前の手順で *EffectiveDateTime* を前の手順で設定して、段階的に実行することを検討してください `datetime` 。

### <a name="data-partitioning-example"></a>データのパーティション分割の例

2つのパーティションキーを持つデータパーティション分割ポリシーオブジェクト。
1. `string`という名前の型指定された列に対するハッシュパーティションキー `tenant_id` 。
    * この例では、 `XxHash64` が256で、既定値がであるハッシュ関数が使用されて `MaxPartitionCount` `Seed` `1` います。
1. `datetime`という名前の型列に対する uniform datetime 範囲パーティションキー `timestamp` 。
    * を `datetime(1970-01-01)` 参照ポイントとして使用し、各パーティションにサイズを指定し `1d` ます。

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
        "RangeSize": "1.00:00:00",
        "OverrideCreationTime": false
      }
    }
  ]
}
```

## <a name="additional-properties"></a>追加のプロパティ

次のプロパティは、ポリシーの一部として定義できます。 これらのプロパティは省略可能であり、変更しないことをお勧めします。

|プロパティ | 説明 | 推奨値 | 既定値 |
|---|---|---|---|
| **MinRowCountPerOperation** |  1つのデータパーティション分割操作のソースエクステントの行数の合計の最小ターゲット。 | | `0` |
| **MaxRowCountPerOperation** |  1つのデータパーティション分割操作のソースエクステントの行数の合計の最大ターゲット。 | パーティション分割操作で大量のメモリまたは CPU 使用率が消費される場合は、5分よりも小さい値を設定します。 詳細については、「 [monitoring](#monitor-partitioning)」を参照してください。 | `0`。既定のターゲットは500万レコードです。 |

## <a name="the-data-partitioning-process"></a>データパーティション分割プロセス

* データのパーティション分割は、クラスター内のインジェスト後のバックグラウンドプロセスとして実行されます。
  * に継続的に取り込まれたされるテーブルには、まだパーティション分割されていないデータの "後部" が常に存在している必要があります (非同種エクステント)。
* データのパーティション分割は、ポリシーのプロパティの値に関係なく、ホットエクステントでのみ実行さ `EffectiveDateTime` れます。
  * コールドエクステントをパーティション分割する必要がある場合は、 [キャッシュポリシー](cachepolicy.md)を一時的に調整する必要があります。

## <a name="monitor-partitioning"></a>パーティション分割の監視

[`.show diagnostics`](../management/diagnostics.md#show-diagnostics)クラスター内のパーティション分割の進行状況または状態を監視するには、コマンドを使用します。

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

出力には次のものが含まれます。

  * `MinPartitioningPercentageInSingleTable`: クラスター内にデータのパーティション分割ポリシーがあるすべてのテーブルにわたる、パーティション分割されたデータの最小割合。
    * この割合が常に90% 未満の場合は、クラスターのパーティション分割 [容量](partitioningpolicy.md#partition-capacity)を評価します。
  * `TableWithMinPartitioningPercentage`: パーティションの割合が上に表示されるテーブルの完全修飾名。

[`.show commands`](commands.md)パーティション分割コマンドとそのリソースの使用を監視するには、を使用します。 次に例を示します。

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

## <a name="partition-capacity"></a>パーティション容量

* データのパーティション分割プロセスにより、より多くのエクステントが作成されます。 クラスターによって、エクステントの [マージ容量](../management/capacitypolicy.md#extents-merge-capacity)が徐々に増加し、 [エクステントをマージ](../management/extents-overview.md) するプロセスが維持される可能性があります。
* 大量のインジェストが発生した場合、またはパーティション分割ポリシーが定義されているテーブルの数が多い場合、クラスターはエクステントの [パーティション容量](../management/capacitypolicy.md#extents-partition-capacity)を徐々に増やし、 [エクステントをパーティション分割するプロセス](#the-data-partitioning-process) を継続できるようにします。
* リソースが過剰に消費されるのを防ぐために、これらの動的な増加は制限されています。 完全に使用されていれば、上限を超えて徐々に増加することが必要になる場合があります。
  * 容量を増やすと[、クラスターの](../../manage-cluster-vertical-scaling.md)リソースの使用が大幅に増加する場合は、 / 手動で、または自動スケールを有効にすることで、クラスターをスケール[アウト](../../manage-cluster-horizontal-scaling.md)することができます。

## <a name="outliers-in-partitioned-columns"></a>パーティション分割された列の外れ値

* 次の状況では、クラスターのノード間でのデータの分散分散が不安定になり、クエリのパフォーマンスが低下する可能性があります。
    * ハッシュパーティションキーに他の値よりもはるかに多くの値が含まれている場合 (たとえば、空の文字列の場合) や、ジェネリック値 ( `null` やなど `N/A` )。
    * 値は、 `tenant_id` データセット内でより一般的なエンティティ (など) を表します。
* Uniform range datetime パーティションキーの値の大部分が、列のほとんどの値からの "遠く" である場合、データのパーティション分割プロセスのオーバーヘッドが増加し、クラスターが追跡する必要がある多数の小さなエクステントにつながる可能性があります。 このような状況の例として、過去または将来の datetime 値があります。

どちらの場合も、データを "修正" するか、インジェストの前または後にデータ内の不要なレコードをフィルターで除外して、クラスターでのデータのパーティション分割のオーバーヘッドを軽減します。 たとえば、 [更新ポリシー](updatepolicy.md)を使用します。

## <a name="next-steps"></a>次のステップ

[パーティション分割ポリシー制御コマンド](../management/partitioning-policy.md)を使用して、テーブルのデータのパーティション分割ポリシーを管理します。
