---
title: quantize_udf ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの quantize_udf () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 19d35e70ddd47b1b0390778a0e775a147235c587
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557932"
---
# <a name="quantize_udf"></a>quantize_udf ()


関数 `quantize_udf()` ビンのメトリック列。 メトリック列をカテゴリラベルに quantizes し、K の意味アルゴリズムに基づいています。

> [!NOTE]
>* この関数にはインライン Python が含まれており、クラスターで [python () プラグインを有効にする](../query/pythonplugin.md#enable-the-plugin) 必要があります。
>* この関数は、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`T | invoke quantize_udf(`*num_bins* `,`*in_cols* `,`*out_cols* `,`*ラベル*`)`

## <a name="arguments"></a>引数

* *num_bins*: ビン数が必要です。
* *in_cols*: 量子化する列の名前を格納している動的配列。
* *out_cols*: ビン分割された値の各出力列の名前を格納している動的配列。
* *ラベル*: ラベル名を含む動的配列。 このパラメーターは省略可能です。 *ラベル*が指定されていない場合は、ビン範囲が使用されます。

## <a name="usage"></a>使用法

* `quantize_udf()` はユーザー定義関数です。 クエリにコードを埋め込むか、データベースにインストールすることができます。
    * アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。
    * 繰り返し使用する場合は、 [. create function](../management/create-function.md)を使用して永続化します。 <br>
        関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。
* `quantize_udf()`は[表形式関数](../query/functions/user-defined-functions.md#tabular-function)で、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されます。

# <a name="ad-hoc-usage"></a>[アドホック使用](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let qunatize_udf=(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic=dynamic(null))
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

# <a name="persistent-usage"></a>[永続的な使用](#tab/persistent)

* **1 回限りのインストール**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "Binning metric columns")
qunatize_udf(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic)
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

* **使用方法**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
x    x_label    x_bin
1    Low        1.0-7.75
2    Low        1.0-7.75
3    Low        1.0-7.75
4    Low        1.0-7.75
5    Low        1.0-7.75
20   High       17.5-25.0
21   High       17.5-25.0
22   High       17.5-25.0
23   High       17.5-25.0
24   High       17.5-25.0
25   High       17.5-25.0
10   Med        7.75-17.5
11   Med        7.75-17.5
12   Med        7.75-17.5
13   Med        7.75-17.5
14   Med        7.75-17.5
15   Med        7.75-17.5
```
