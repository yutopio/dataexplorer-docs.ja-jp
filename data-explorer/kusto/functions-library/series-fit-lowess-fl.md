---
title: series_fit_lowess_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_lowess_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/29/2020
no-loc: LOWESS
ms.openlocfilehash: 9a72905820a55f2fbd6f200514cac69450aa9277
ms.sourcegitcommit: 335e05864e18616c10881db4ef232b9cda285d6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97596805"
---
# <a name="series_fit_lowess_fl"></a>series_fit_lowess_fl()

関数は、 `series_fit_lowess_fl()` 系列に[ LOWESS 回帰](https://www.wikipedia.org/wiki/Local_regression)を適用します。 この関数は、複数の系列 (動的な数値配列) を含むテーブルを取得し、 *LOWESS 曲線* を生成します。これは、元の系列のスムージングされたバージョンです。

> [!NOTE]
> * `series_fit_lowess_fl()` は [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。
> * この関数にはインライン Python が含まれており、クラスターで [python () プラグインを有効にする](../query/pythonplugin.md#enable-the-plugin) 必要があります。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`T | invoke series_fit_lowess_fl(`*y_series* `,`*y_fit_series* `, [`*fit_size* `, `*x_series* `,`*x_istime*]`)`

## <a name="arguments"></a>引数

* *y_series*: [依存変数](https://www.wikipedia.org/wiki/Dependent_and_independent_variables)を含む入力テーブル列の名前。 この列は、対応する系列です。
* *y_fit_series*: 適合する系列を格納する列の名前。
* *fit_size*: 各ポイントについて、ローカル回帰はそれぞれの *fit_size* 最も近いポイントに適用されます。 このパラメーターは省略可能で、既定値は *5* です。
* *x_series*: [独立変数](https://www.wikipedia.org/wiki/Dependent_and_independent_variables)、つまり x または時間軸を含む列の名前。 このパラメーターは省略可能であり、均等の [時系列](https://www.wikipedia.org/wiki/Unevenly_spaced_time_series)に対してのみ必要です。 既定値は空の文字列です。 x は均等に並んだ系列の回帰に対して冗長です。
* *x_istime*: このブール型パラメーターは、 *x_series* が指定されていて、それが datetime のベクターである場合にのみ必要です。 このパラメーターは省略可能で、既定値は *False* です。

## <a name="usage"></a>使用法

`series_fit_lowess_fl()` は、ユーザー定義関数の [表形式関数](../query/functions/user-defined-functions.md#tabular-function)であり、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されます。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_lowess_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的な使用方法については、を使用 [`.create function`](../management/create-function.md) します。  関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fits a local polynomial using LOWESS method to a series")
series_fit_lowess_fl(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用法

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

---

:::image type="content" source="images/series-fit-lowess-fl/lowess-regular-time-series.png" alt-text="Graph showing nine points :::no loc (LOWESS)::: 通常の時系列に合わせる "border =" false ":::

## <a name="examples"></a>例

次の例は、関数が既にインストールされていることを前提としています。

### <a name="test-irregular-time-series"></a>不規則な時系列をテストする

次の例では、不規則 (均等間隔) の時系列をテストします。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let max_t = datetime(2016-09-03);
demo_make_series1
| where TimeStamp between ((max_t-1d)..max_t)
| summarize num=count() by bin(TimeStamp, 5m), OsVer
| order by TimeStamp asc
| where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create irregular time series
| summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9, 'TimeStamp', True)
| render timechart 
```

:::image type="content" source="images/series-fit-lowess-fl/lowess-irregular-time-series.png" alt-text="Graph showing nine points :::no loc (LOWESS)::: 不規則なタイムシリーズに適合する "border =" false ":::

比較と LOWESS 多項式フィット

次の例には、x 軸と y 軸にノイズがある5番目の注文多項式が含まれています。 と多項式適合の比較を参照してください LOWESS 。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend y_lowess = dynamic(null)
| invoke series_fit_lowess_fl('y', 'y_lowess', 15, 'x')
| extend series_fit_poly(y, x, 5)
| project x, y, y_lowess, y_polynomial=series_fit_poly_y_poly_fit
| render linechart
```

:::image type="content" source="images/series-fit-lowess-fl/lowess-vs-poly-fifth-order-noise.png" alt-text="Graphs of :::x & y 軸でのノイズを含む5番目の注文多項式に対して、loc (LOWESS)::: vs の多項式フィットが発生する場合::::
