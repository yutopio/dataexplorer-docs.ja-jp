---
title: kmeans_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの kmeans_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: b56aefd94ed58b8731ea6237f131b4da74d4f7be
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92184165"
---
# <a name="kmeans_fl"></a>kmeans_fl ()

関数は、 `kmeans_fl()` [k の意味アルゴリズム](https://en.wikipedia.org/wiki/K-means_clustering)を使用してデータセットを使用します。

> [!NOTE]
> * `kmeans_fl()` は [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。
> * この関数にはインライン Python が含まれており、クラスターで [python () プラグインを有効にする](../query/pythonplugin.md#enable-the-plugin) 必要があります。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`T | invoke kmeans_fl(`*k* `,` *features_cols* `,` *cluster_col*`)`

## <a name="arguments"></a>引数

* *k*: 必要なクラスター数。
* *features_cols*: クラスタリングに使用される機能列の名前を格納している動的配列。
* *cluster_col*: 各レコードの出力クラスター ID を格納する列の名前。

## <a name="usage"></a>使用方法

`kmeans_fl()`は、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されるユーザー定義の[表形式関数](../query/functions/user-defined-functions.md#tabular-function)です。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let kmeans_fl=(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的に使用するには、 [. create 関数](../management/create-function.md)を使用します。 関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1 回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "K-Means clustering")
kmeans_fl(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用方法

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Timestamp                   Temperature Humidity    Light  CO2         HumidityRatio Occupancy Test  cluster_id
2015-02-02 14:38:00.0000000 23.64       27.1        473    908.8       0.00489763    TRUE      TRUE  1
2015-02-03 01:47:00.0000000 20.575      22.125      0      446         0.00330878    FALSE     TRUE  0
2015-02-10 08:47:00.0000000 20.42666667 33.56       405    494.3333333 0.004986493   TRUE      FALSE 4
2015-02-10 09:15:00.0000000 20.85666667 35.09666667 433    665.3333333 0.005358055   TRUE      FALSE 4
2015-02-11 16:13:00.0000000 21.89       30.0225     429    771.75      0.004879358   TRUE      TRUE  4
2015-02-13 14:06:00.0000000 23.4175     26.5225     608    599.75      0.004728116   TRUE      TRUE  4
2015-02-13 23:09:00.0000000 20.13333333 32.2        0      502.6666667 0.004696278   FALSE     TRUE  0
2015-02-15 18:30:00.0000000 20.5        32.79       0      666.5       0.004893459   FALSE     TRUE  3
2015-02-17 13:43:00.0000000 21.7        33.9        454    1167        0.005450924   TRUE      TRUE  1
2015-02-17 18:17:00.0000000 22.025      34.2225     0      1538.25     0.005614538   FALSE     TRUE  2
```

関数が既にインストールされている場合は、各クラスターの重心とサイズを抽出します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| summarize Temperature=avg(Temperature), Humidity=avg(Humidity), Light=avg(Light), CO2=avg(CO2), HumidityRatio=avg(HumidityRatio), num=count() by cluster_id
| order by num

```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
cluster_id  Temperature        Humidity            Light            CO2                HumidityRatio        num
0           20.3507186863278   27.1521395395781    10.1995789883291 486.804272186974   0.00400132147662714  11124
4           20.9247315268427   28.7971160082823    20.7311894656536 748.965771574469   0.00440412568299058  3063
1           22.0284137970445   27.8953334469889    481.872136037748 1020.70779349773   0.00456692559904535  2514
3           22.0344177115763   25.1151053429273    462.358969056434 656.310608696507   0.00411782436443015  2176
2           21.4091216082295   31.8363099552939    174.614816229606 1482.05062388414   0.00504573022875817  1683
```
