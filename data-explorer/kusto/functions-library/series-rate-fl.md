---
title: series_rate_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_rate_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 9d34fa3d880b4888cd7f43913644e9cba8b4ca9d
ms.sourcegitcommit: 335e05864e18616c10881db4ef232b9cda285d6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97596840"
---
# <a name="series_rate_fl"></a>series_rate_fl()


関数は、 `series_rate_fl()` 1 秒あたりのメトリック増加率の平均値を計算します。 このロジックは、PromQL [rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) 関数に従います。 これは、 [Prometheus](https://prometheus.io/) 監視システムによって Azure データエクスプローラーに取り込まれたし、 [series_metric_fl ()](series-metric-fl.md)によって取得される、カウンターメトリックスの時系列に使用する必要があります。

> [!NOTE]
>`series_rate_fl()` は [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`T | invoke series_rate_fl(`*n_bins*`)`

`T`[series_metric_fl ()](series-metric-fl.md)から返されるテーブルです。 そのスキーマにはが含まれ `(timestamp:dynamic, name:string, labels:string, value:dynamic)` ます。

## <a name="arguments"></a>引数

* *n_bins*: 比率を計算するために抽出されたメトリック値の間隔を指定するビンの数。 関数は、現在のサンプルとそれ以前の *n_bins* の差を計算し、それぞれのタイムスタンプの差 (秒単位) で除算します。 このパラメーターは省略可能で、既定値は1ビンです。 既定の設定では、 [irate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)、PromQL の瞬間レート関数が計算されます。
* *fix_reset*: カウンターのリセットをチェックするかどうかを制御し、PromQL [rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) 関数のように修正するかどうかを制御するブール型のフラグ。 このパラメーターは省略可能で、既定値は `true` です。 `false`リセットを確認する必要がない場合に備えて冗長な分析を保存するには、をに設定します。

## <a name="usage"></a>使用法

`series_rate_fl()` は、ユーザー定義の [表形式関数](../query/functions/user-defined-functions.md#tabular-function)であり、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されます。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

> [!NOTE]
> [series_metric_fl ()](series-metric-fl.md) は、関数の一部として使用され、インストールまたは埋め込まれている必要があります。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, value:dynamic), n_bins:int=1, fix_reset:bool=true)
{
    tbl
    | where fix_reset                                                   //  Prometheus counters can only go up
    | mv-apply value to typeof(double) on   
    ( extend correction = iff(value < prev(value), prev(value), 0.0)    // if the value decreases we assume it was reset to 0, so add last value
    | extend cum_correction = row_cumsum(correction)
    | extend corrected_value = value + cum_correction
    | summarize value = make_list(corrected_value))
    | union (tbl | where not(fix_reset))
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend rate = series_divide(dv, dt)
    | project-away dt, dv, timestampS, value, valueS
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的な使用方法については、を使用 [`.create function`](../management/create-function.md) します。 関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1 回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, value:dynamic), n_bins:int=1, fix_reset:bool=true)
{
    tbl
    | where fix_reset                                                   //  Prometheus counters can only go up
    | mv-apply value to typeof(double) on   
    ( extend correction = iff(value < prev(value), prev(value), 0.0)    // if the value decreases we assume it was reset to 0, so add last value
    | extend cum_correction = row_cumsum(correction)
    | extend corrected_value = value + cum_correction
    | summarize value = make_list(corrected_value))
    | union (tbl | where not(fix_reset))
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend rate = series_divide(dv, dt)
    | project-away dt, dv, timestampS, value, valueS
}
```

### <a name="usage"></a>使用法

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="すべてのディスクのディスク書き込みメトリックの1秒あたりの割合を示すグラフ" border="false":::

## <a name="example"></a>例

次の例では、2つのホストのメインディスクを選択し、その関数が既にインストールされていることを前提としています。 この例では、最初のパラメーターとして入力テーブルを指定して、代替の直接呼び出し構文を使用します。
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="過去2時間のメインディスク書き込みメトリックの1秒あたりの割合を10ビン間隔で示すグラフ" border="false":::
