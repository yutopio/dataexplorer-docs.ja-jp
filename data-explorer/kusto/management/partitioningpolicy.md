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
ms.openlocfilehash: 6ce7cf38c88879b52c4e2e259e3e9a5cade959de
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907148"
---
# <a name="data-partitioning-policy-preview"></a>データのパーティション分割ポリシー (プレビュー)

パーティション分割ポリシーでは、特定のテーブルに対して[エクステント (データシャード)](../management/extents-overview.md)をどのようにパーティション分割するかを定義します。

> [!NOTE]
> データのパーティション分割機能は*プレビュー*段階です。

ポリシーの主な目的は、パーティション分割された列の値の小さなサブセットに絞り込まれることがわかっているクエリのパフォーマンスを向上させること、または高カーディナリティ文字列列に対して集計/結合を行うことです。 2つ目の利点は、データの圧縮が優れていることです。

> [!WARNING]
> ポリシーを定義できるテーブルの量には、ハードコーディングされた制限はありませんが、追加のテーブルごとに、クラスターのノードで実行されているバックグラウンドデータパーティション処理のオーバーヘッドが増加し、クラスターからの追加リソースが必要になる場合があります。「[容量](#capacity)」を参照してください。

## <a name="partition-keys"></a>パーティション キー

次の種類のパーティションキーがサポートされています。

|種類                                                   |列の型 |パーティションのプロパティ                    |パーティションの値                                        |
|-------------------------------------------------------|------------|----------------------------------------|-------------------------------------------------------|
|[ハッシュ](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[統一範囲](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>ハッシュパーティションキー

テーブルの型指定された`string`列にハッシュパーティションキーを適用するのは`application_ID` `tenant_ID` 、大*規模なディメンション*の特定`==` `string`の`in()`型の列 (、、など) に対して*大部分*のクエリで等値フィルター (,) と集計/結合を使用する場合に適し`user_ID`ています。

* ハッシュ剰余関数は、データをパーティション分割するために使用されます。
* 同じパーティションに属するすべての*同種*(パーティション分割) エクステントは、同じデータノードに割り当てられます。
* *同種*(パーティション分割) エクステントのデータは、ハッシュパーティションキーによって並べ替えられます。
  * テーブルでパーティションキーが定義されている場合は、[行順序ポリシー](roworderpolicy.md)にパーティションキーを含める必要はありません。
* [シャッフル戦略](../query/shufflequery.md)を使用`shuffle key`し、で`join`使用される、 `summarize`または`make-series`テーブルのハッシュパーティションキーとして使用されるクエリは、クラスターノード間での移動に必要なデータ量が大幅に減少するため、パフォーマンスが向上することが期待されます。

#### <a name="partition-properties"></a>パーティションのプロパティ

* `Function`使用するハッシュ剰余関数の名前を指定します。
  * サポートされ`XxHash64`ている値:。
* `MaxPartitionCount`期間ごとに作成するパーティションの最大数 (ハッシュ剰余関数の剰余引数) です。
  * サポートされている値`(1,1024]`は、範囲内の値です。
    * 値は次のようになる必要があります。
      * クラスター内のノード数より大きい
      * 列のカーディナリティより小さい。
    * 値が大きいほど、クラスターのノードでのデータパーティション分割プロセスのオーバーヘッドが大きくなり、各期間のエクステントの数が増加しますが、
    * 先頭に使用することをお`256`勧めする値はです。
      * 必要に応じて、前述の考慮事項に基づいて (通常は上位に) 調整できます。また、クエリのパフォーマンスのメリットと、データの取り込み後のパーティション分割のオーバーヘッドの測定に基づいて調整することもできます。
* `Seed`ハッシュ値のランダム化するに使用する値を指定します。
  * 値は正の整数である必要があります。
  * 推奨値 (既定では、指定されて`1`いない場合) の値はです。

#### <a name="example"></a>例

次に示すのは、型指定さ`string` `tenant_id`れた列に対するハッシュパーティションキーです。

この例で`XxHash64`は、ハッシュ関数を`MaxPartitionCount` `256`、の、および既定`Seed`の`1`を使用して使用しています。

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

テーブル内の型指定された列`datetime`に対して uniform range datetime パーティションキーを適用することは、テーブルへのデータ取り込まれたがこの列に従って並べ替えられる*可能性が低い*場合に適しています。 このような場合は、エクステント間でデータをシャッフルして、各エクステントが一定期間のレコードを含むようにすると便利な場合があります。 `datetime`これにより、クエリの実行時に列のフィルターが向上します。

* 使用されるパーティション関数は[bin_at ()](../query/binatfunction.md)であり、カスタマイズすることはできません。

#### <a name="partition-properties"></a>パーティションのプロパティ

* `RangeSize`各 datetime `timespan`パーティションのサイズを示すスカラー定数を指定します。
* `Reference`は、 `datetime`固定された時刻を示す型のスカラー定数で、どの datetime パーティションがアラインされているかを示します。
  * Datetime パーティションキーに値が`null`あるレコードがある場合、そのパーティション値はの`Reference`値に設定されます。

#### <a name="example"></a>例

次に示すのは、型指定された`datetime`列に対する uniform datetime 範囲パーティションキーです。`timestamp`

を参照`datetime(1970-01-01)`ポイントとして使用し、各パーティション`1d`にサイズを指定します。

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

既定では、テーブルのデータのパーティション分割`null`ポリシーはです。この場合、テーブル内のデータはパーティション分割されません。

データのパーティション分割ポリシーには、次の主要なプロパティがあります。

* **Partitionkeys**:
  * テーブル内のデータをパーティション分割する方法を定義する[パーティションキー](#partition-keys)のコレクション。
  * テーブルは、次の 3 `2`つのオプションのいずれかを使用して、最大でキーをパーティション分割できます。
    * 1[ハッシュパーティションキー](#hash-partition-key)。
    * 1 [uniform range datetime パーティションキー](#uniform-range-datetime-partition-key)。
    * 1[ハッシュパーティションキー](#hash-partition-key)と1個の[均一な範囲の datetime パーティションキー](#uniform-range-datetime-partition-key)。
  * 各パーティションキーには、次のプロパティがあります。
    * `ColumnName`: `string `データのパーティション分割に使用される列の名前。
    * `Kind`: `string` -適用するデータパーティションの種類 (`Hash`また`UniformRange`は)。
    * `Properties`: `property bag` -パーティション分割の実行に応じてパラメーターを定義します。

* **EffectiveDateTime**:
  * ポリシーが有効になる UTC datetime。
  * このプロパティは*省略可能*です。指定されていない場合、ポリシーは、ポリシーが適用された後、データ取り込まれたで有効になります。
  * すぐにバインドされる非同種 (パーティション分割されていない) エクステントは、リテンション期間によって破棄されます (つまり、テーブルの有効な論理的な削除期間の90% より前の作成時間は、パーティション分割プロセスによって無視されます)。

### <a name="example"></a>例

次に示すのは、2つのパーティションキーを持つデータパーティションポリシーオブジェクトです。
1. という名前`tenant_id`の型指定`string`された列に対するハッシュパーティションキー。
    - この例で`XxHash64`は、が`MaxPartitionCount` 256 で、既定値`Seed`がであるハッシュ`1`関数が使用されています。
2. 型指定された列に`datetime`対する uniform datetime 範囲のパーティションキー`timestamp`
    - を参照`datetime(1970-01-01)`ポイントとして使用し、各パーティション`1d`にサイズを指定します。

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
  * このプロパティは*省略可能*で、既定値は`0`です。

* **MaxRowCountPerOperation**:
  * 1つのデータパーティション分割操作のソースエクステントの行数の合計の最大ターゲット。
  * このプロパティは*省略可能*で、既定値は`0`です (この場合、500万レコードの既定のターゲットが有効になります)。

## <a name="notes"></a>Notes

### <a name="the-data-partitioning-process"></a>データパーティション分割プロセス

* データのパーティション分割は、クラスター内のインジェスト後のバックグラウンドプロセスとして実行されます。
  * 継続的に取り込まれたされているテーブルには、常にパーティション分割されていないデータの "テール" (*非同種*エクステント) があることが想定されています。
* データのパーティション分割は、ポリシーの`EffectiveDateTime`プロパティの値に関係なく、ホットエクステントでのみ実行されます。
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

* データのパーティション分割プロセスによってより多くのエクステントが作成されるため、[エクステント](../management/extents-overview.md)のマージ処理が最大になるように、クラスターの[エクステントのマージ容量](../management/capacitypolicy.md#extents-merge-capacity)を徐々に増やすことが必要になる場合があります。
* 必要な場合 (たとえば、大量のインジェストが発生した場合や、パーティション分割が必要なテーブルの数が多い場合など) は、クラスターの[エクステントパーティション容量](../management/capacitypolicy.md#extents-partition-capacity)を (徐々に直線的に) 増加させて、同時実行のパーティション分割操作の数を増やすことができます。
  * パーティション分割を増やすと、クラスターのリソースの使用が大幅に増加する場合は、手動で、または自動スケールを有効にして、クラスターをスケールアップ/スケールアウトします。

### <a name="outliers-in-partitioned-columns"></a>パーティション分割された列の外れ値

* ハッシュパーティションキーの値の割合が、適切に設定されていない (たとえば、空であるか、汎用値がある) 場合、クラスターのノード間で分散されていないデータの分布が発生する可能性があります。
* Uniform range datetime パーティションキーの値の大部分が、列のほとんどの値 (たとえば、遠く以上の datetime 値) からの "遠く" の大きさである場合。

どちらの場合も、データを "修正" するか、インジェストの前または後に ([更新ポリシー](updatepolicy.md)を使用して) データ内の関係のないレコードをフィルターで除外して、クラスターでのデータのパーティション分割のオーバーヘッドを軽減する必要があります。

## <a name="next-steps"></a>次のステップ

[パーティション分割ポリシー制御コマンド](../management/partitioning-policy.md)を使用して、テーブルのデータのパーティション分割ポリシーを管理します。
