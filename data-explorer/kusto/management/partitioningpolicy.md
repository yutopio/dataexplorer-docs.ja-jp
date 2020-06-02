---
title: データのパーティション分割ポリシー (プレビュー)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのデータのパーティション分割ポリシー (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 829e23fc087e732db4a555f3007f760249df15fe
ms.sourcegitcommit: d660e39f24bd9a0e1c788cb86d4da9afd981cfc9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268068"
---
# <a name="data-partitioning-policy-preview"></a>データのパーティション分割ポリシー (プレビュー)

パーティション分割ポリシーでは、特定のテーブルに対して[エクステント (データシャード)](../management/extents-overview.md)をどのようにパーティション分割するかを定義します。

ポリシーの主な目的は、パーティション分割された列の値の小さなサブセットに絞り込まれることがわかっているクエリのパフォーマンスを向上させること、または高カーディナリティ文字列列に対して集計/結合を行うことです。 2つ目の利点は、データの圧縮が優れていることです。

> [!CAUTION]
> ポリシーを定義できるテーブルの量には、ハードコーディングされた制限はありませんが、追加のテーブルごとに、クラスターのノードで実行されているバックグラウンドデータパーティション処理のオーバーヘッドが増加し、クラスターからの追加リソースが必要になる場合があります。「[容量](#capacity)」を参照してください。

## <a name="partition-keys"></a>パーティション キー

次の種類のパーティションキーがサポートされています。

|種類                                                   |列の型 |パーティションのプロパティ                    |パーティションの値                                        |
|-------------------------------------------------------|------------|----------------------------------------|-------------------------------------------------------|
|[Hash](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範囲](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>ハッシュパーティションキー

テーブルの型指定された列にハッシュパーティションキーを適用 `string` するのは*majority* 、大 `==` `in()` 規模な `string` *ディメンション*の特定の型の列 (、、など) に対して大部分のクエリで等値フィルター (,) と集計/結合を使用する場合に適してい `application_ID` `tenant_ID` `user_ID` ます。

* ハッシュ剰余関数は、データをパーティション分割するために使用されます。
* 同じパーティションに属するすべての*同種*(パーティション分割) エクステントは、同じデータノードに割り当てられます。
* *同種*(パーティション分割) エクステントのデータは、ハッシュパーティションキーによって並べ替えられます。
  * テーブルでパーティションキーが定義されている場合は、[行順序ポリシー](roworderpolicy.md)にパーティションキーを含める必要はありません。
* [シャッフル戦略](../query/shufflequery.md)を使用し、 `shuffle key` で使用される、またはテーブルのハッシュパーティションキーとして使用されるクエリは、 `join` `summarize` `make-series` クラスターノード間での移動に必要なデータ量が大幅に減少するため、パフォーマンスが向上することが期待されます。

#### <a name="partition-properties"></a>パーティションのプロパティ

* `Function`使用するハッシュ剰余関数の名前を指定します。
  * サポートされている値: `XxHash64` 。
* `MaxPartitionCount`期間ごとに作成するパーティションの最大数 (ハッシュ剰余関数の剰余引数) です。
  * サポートされている値は、範囲内の値 `(1,1024]` です。
    * 値は次のようになる必要があります。
      * クラスター内のノード数より大きい
      * 列のカーディナリティより小さい。
    * 値が大きいほど、クラスターのノードでのデータパーティション分割プロセスのオーバーヘッドが大きくなり、各期間のエクステントの数が増加しますが、
    * 先頭に使用することをお勧めする値は `256` です。
      * 必要に応じて、前述の考慮事項に基づいて (通常は上位に) 調整できます。また、クエリのパフォーマンスのメリットと、データの取り込み後のパーティション分割のオーバーヘッドの測定に基づいて調整することもできます。
* `Seed`ハッシュ値のランダム化するに使用する値を指定します。
  * 値は正の整数である必要があります。
  * 推奨値 (既定では、指定されていない場合) の値はです `1` 。

#### <a name="example"></a>例

次に示すのは、型指定された列に対するハッシュパーティションキー `string` `tenant_id` です。

この例では、ハッシュ関数を、の、および既定のを使用して使用して `XxHash64` `MaxPartitionCount` `256` `Seed` `1` います。

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>Uniform range datetime パーティションキー

テーブル内の型指定された列に対して uniform range datetime パーティションキーを適用する `datetime` ことは、テーブルへのデータ取り込まれたがこの列に従って並べ替えられる*可能性が低い*場合に適しています。 このような場合は、エクステント間でデータをシャッフルして、各エクステントが一定期間のレコードを含むようにすると便利な場合があります。これにより、クエリの実行時に列のフィルターが向上 `datetime` します。

* 使用されるパーティション関数は[bin_at ()](../query/binatfunction.md)であり、カスタマイズすることはできません。

#### <a name="partition-properties"></a>パーティションのプロパティ

* `RangeSize``timespan`各 datetime パーティションのサイズを示すスカラー定数を指定します。
  * 最初の推奨値は、 `1.00:00:00` (1 日) です。
  * 値を設定することは推奨されて*いません*。これは、テーブルに大量の小さなエクステントが含まれていて、結合できない場合に発生する可能性があるためです。
* `Reference`は、 `datetime` 固定された時刻を示す型のスカラー定数で、どの datetime パーティションがアラインされているかを示します。
  * 先頭に使用することをお勧めする値は `1970-01-01 00:00:00` です。
  * Datetime パーティションキーに値があるレコードがある場合 `null` 、そのパーティション値はの値に設定され `Reference` ます。

#### <a name="example"></a>例

次に示すのは、型指定された列に対する uniform datetime 範囲パーティションキーです。 `datetime``timestamp`

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
  * テーブル内のデータをパーティション分割する方法を定義する[パーティションキー](#partition-keys)のコレクション。
  * テーブルは `2` 、次の3つのオプションのいずれかを使用して、最大でキーをパーティション分割できます。
    * 1[ハッシュパーティションキー](#hash-partition-key)。
    * 1 [uniform range datetime パーティションキー](#uniform-range-datetime-partition-key)。
    * 1[ハッシュパーティションキー](#hash-partition-key)と1個の[均一な範囲の datetime パーティションキー](#uniform-range-datetime-partition-key)。
  * 各パーティションキーには、次のプロパティがあります。
    * `ColumnName`: `string ` データのパーティション分割に使用される列の名前。
    * `Kind`: `string` -適用するデータパーティションの種類 ( `Hash` または `UniformRange` )。
    * `Properties`: `property bag` -パーティション分割の実行に応じてパラメーターを定義します。

* **EffectiveDateTime**:
  * ポリシーが有効になる UTC datetime。
  * このプロパティは*省略可能*です。指定されていない場合、ポリシーは、ポリシーが適用された後、データ取り込まれたで有効になります。
  * すぐにバインドされる非同種 (パーティション分割されていない) エクステントは、リテンション期間によって破棄されます (つまり、テーブルの有効な論理的な削除期間の90% より前の作成時間は、パーティション分割プロセスによって無視されます)。

### <a name="example"></a>例

次に示すのは、2つのパーティションキーを持つデータパーティションポリシーオブジェクトです。
1. `string`という名前の型指定された列に対するハッシュパーティションキー `tenant_id` 。
    - この例では、 `XxHash64` が256で、既定値がであるハッシュ関数が使用されて `MaxPartitionCount` `Seed` `1` います。
2. 型指定された列に対する uniform datetime 範囲のパーティションキー `datetime``timestamp`
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
        "Seed": 1
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

次のプロパティは、ポリシーの一部として定義できますが、*省略可能*であり、変更することはお勧めしません。

* **MinRowCountPerOperation**:
  * 1つのデータパーティション分割操作のソースエクステントの行数の合計の最小ターゲット。
  * このプロパティは*省略可能*で、既定値は `0` です。

* **MaxRowCountPerOperation**:
  * 1つのデータパーティション分割操作のソースエクステントの行数の合計の最大ターゲット。
  * このプロパティは*省略可能*で、既定値はです `0` (この場合、500万レコードの既定のターゲットが有効になります)。

## <a name="notes"></a>Notes

### <a name="the-data-partitioning-process"></a>データパーティション分割プロセス

* データのパーティション分割は、クラスター内のインジェスト後のバックグラウンドプロセスとして実行されます。
  * 継続的に取り込まれたされているテーブルには、常にパーティション分割されていないデータの "テール" (*非同種*エクステント) があることが想定されています。
* データのパーティション分割は、ポリシーのプロパティの値に関係なく、ホットエクステントでのみ実行さ `EffectiveDateTime` れます。
  * コールドエクステントをパーティション分割する必要がある場合は、それに応じて[キャッシュポリシー](cachepolicy.md)を一時的に調整する必要があります。

#### <a name="monitoring"></a>監視

* クラスターでパーティション分割の進行状況と状態を監視するには、 [. show diagnostics](../management/diagnostics.md#show-diagnostics)コマンドを使用します。

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable,
          TableWithMinPartitioningPercentage
```

出力には次のものが含まれます。

  * `MinPartitioningPercentageInSingleTable`: クラスター内にデータのパーティション分割ポリシーがあるすべてのテーブルにわたる、パーティション分割されたデータの最小割合。
      * この割合が常に90% 未満の場合は、クラスターのパーティション分割容量を評価します ([下記](partitioningpolicy.md#capacity)参照)。
  * `TableWithMinPartitioningPercentage`: パーティションの割合が上に表示されるテーブルの完全修飾名。

#### <a name="capacity"></a>容量

* データのパーティション分割プロセスにより、より多くのエクステントが作成されます。 クラスターによって、エクステントの[マージ容量](../management/capacitypolicy.md#extents-merge-capacity)が徐々に増加し、[エクステントをマージ](../management/extents-overview.md)するプロセスが維持される可能性があります。
* 大量のインジェストが発生した場合、またはパーティション分割ポリシーが定義されているテーブルの数が多い場合、クラスターはエクステントの[パーティション容量](../management/capacitypolicy.md#extents-partition-capacity)を徐々に増やして、[エクステントをパーティション分割するプロセス](#the-data-partitioning-process)を継続できるようにします。
* リソースが過剰に消費されるのを防ぐために、これらの動的な増加は制限されています。 上限を超えた場合は、上限を超えるまで (徐々に直線的に) 増やす必要があります。
  * 容量を増やすと[、クラスターの](../../manage-cluster-vertical-scaling.md)リソースの使用が大幅に増加する場合は、 / 手動で、または自動スケールを有効にして、クラスターをスケール[アウト](../../manage-cluster-horizontal-scaling.md)することができます。

### <a name="outliers-in-partitioned-columns"></a>パーティション分割された列の外れ値

* たとえば、空の文字列、ジェネリック値 (やなど) のような値がハッシュパーティションキーに含まれている場合、または、 `null` `N/A` データセットの中で最も普及しているエンティティ (など) を表している場合は、 `tenant_id` クラスターのノード間でのデータの分散分散に寄与し、クエリのパフォーマンスが低下する可能性があります。
* Uniform range datetime パーティションキーの値の大部分が、列の値の大部分から "遠く" (たとえば、過去または将来の datetime 値) である場合、データのパーティション分割プロセスのオーバーヘッドが増加し、クラスターで追跡する必要がある多数の小さなエクステントにつながる可能性があります。

どちらの場合も、データを "修正" するか、インジェストの前または後に ([更新ポリシー](updatepolicy.md)を使用して) データ内の関係のないレコードをフィルターで除外して、クラスターでのデータのパーティション分割のオーバーヘッドを軽減する必要があります。

## <a name="next-steps"></a>次のステップ

[パーティション分割ポリシー制御コマンド](../management/partitioning-policy.md)を使用して、テーブルのデータのパーティション分割ポリシーを管理します。
