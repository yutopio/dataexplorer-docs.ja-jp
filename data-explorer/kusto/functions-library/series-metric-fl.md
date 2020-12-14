---
title: series_metric_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_metric_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 2af82906be2cdbffdc69646a3050376e0a1af867
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389190"
---
# <a name="series_metric_fl"></a>series_metric_fl ()


関数は、 `series_metric_fl()` [Prometheus](https://prometheus.io/) 監視システムを使用して、Azure データエクスプローラーに取り込まれたするメトリックの時系列を選択して取得します。 この関数は、Azure データエクスプローラーに格納されているデータが [Prometheus データモデル](https://prometheus.io/docs/concepts/data_model/)に従って構造化されていることを前提としています。 具体的には、各レコードには次のものが含まれます。
 * timestamp 
 * メトリック名 
 * メトリック値 
 * ラベルの変数セット ( `key:value` ペア)
 
 Prometheus は、メトリック名と個別のラベルのセットで時系列を定義します。 メトリック名とタイムシリーズセレクター (ラベルのセット) を指定することで、 [Prometheus Query Language (PromQL)](https://prometheus.io/docs/prometheus/latest/querying/basics/) を使用して時系列のセットを取得できます。

> [!NOTE]
> `series_metric_fl()` は [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`T | invoke series_metric_fl(`*timestamp_col* `,`*name_col* `,`*labels_col* `,`*value_col* `,`*metric_name* `,`*labels_selector* `,`*元に戻す* `,`*オフセット*`)`

## <a name="arguments"></a>引数

* *timestamp_col*: タイムスタンプを含む列の名前。
* *name_col*: メトリック名を含む列の名前。
* *labels_col*: ラベルディクショナリを含む列の名前。
* *value_col*: メトリック値を含む列の名前。
* *metric_name*: 取得するメトリックタイムシリーズ。
* *labels_selector*: 時間系列セレクター文字列。 [promql に似](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors)ています。 これは、ペアのリストを含む文字列 `key:value` です。たとえば、のように `'key1:val1,key2:val2'` なります。 このパラメーターは省略可能で、既定値は空の文字列です。これは、フィルター処理が行われないことを意味します。 正規表現はサポートされていないことに注意してください。 
* ルック *バック*: 取得する範囲ベクター。 [promql に似](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors)ています。 このパラメーターは省略可能で、既定値は10分です。
* *offset*: [promql と同様に](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier)、現在の時刻からのオフセットを返します。 データは *前 (オフセット)* から取得され、 *前 (オフセット)* に戻ります。 このパラメーターは省略可能で、既定値は0です。これは、データが現在まで () に取得されることを意味します。

## <a name="usage"></a>使用方法

`series_metric_fl()` は、ユーザー定義の [表形式関数](../query/functions/user-defined-functions.md#tabular-function)であり、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されます。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的な使用方法については、を使用 [`.create function`](../management/create-function.md) します。 関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1 回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>使用方法

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="10分間のディスク書き込みメトリックを示すグラフ" border="false":::

## <a name="example"></a>例

次の例では selector が指定されていないため、すべての "書き込み" メトリックが選択されています。 この例では、関数が既にインストールされていることを前提としています。また、最初のパラメーターとして入力テーブルを指定して、代替の直接呼び出し構文を使用します。
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="10分間のすべてのディスクのディスク書き込みメトリックを示すグラフ" border="false":::
