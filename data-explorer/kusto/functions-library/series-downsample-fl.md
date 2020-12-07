---
title: series_downsample_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_downsample_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2020
ms.openlocfilehash: 172d866a84e4718e3d95e8fa646187e79bec65f7
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321864"
---
# <a name="series_downsample_fl"></a>series_downsample_fl()


関数は、 `series_downsample_fl()` [整数係数で時系列を downsamples](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor)します。 この関数は、複数の時系列 (動的な数値配列) を含むテーブルを受け取り、各系列を downsamples します。 出力には、粗い系列とそれぞれの時刻配列の両方が含まれています。 [エイリアス](https://en.wikipedia.org/wiki/Aliasing)を避けるために、関数は、サブサンプリングの前に各系列に単純な[低パスフィルター](https://en.wikipedia.org/wiki/Low-pass_filter)を適用します。

> [!NOTE]
> この関数は、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`T | invoke series_downsample_fl(`*t_col* `,`*y_col* `,`*ds_t_col* `,`*ds_y_col* `,`*sampling_factor*`)`

## <a name="arguments"></a>引数

* *t_col*: データ系列の時間軸を含む、ダウンサンプリングする列の名前 (入力テーブル)。
* *y_col*: ダウンサンプリングする系列を含む列 (入力テーブル) の名前。
* *ds_t_col*: 各系列の downsampled time 軸を格納する列の名前。
* *ds_y_col*: ダウンサンプリングされた系列を格納する列の名前。
* *sampling_factor*: 必要なダウンサンプリングを指定する整数。

## <a name="usage"></a>使用

`series_downsample_fl()` は、ユーザー定義の [表形式関数](../query/functions/user-defined-functions.md#tabular-function)であり、 [invoke 演算子](../query/invokeoperator.md)を使用して適用されます。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_downsample_fl=(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
;
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的な使用方法については、を使用 [`.create function`](../management/create-function.md) します。 関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1 回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Downsampling a series by an integer factor")
series_downsample_fl(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
```

### <a name="usage"></a>使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

---

タイムシリーズ downsampled × 4: :::image type="content" source="images/series-downsample-fl/downsampling-demo.png" alt-text="時系列のダウンサンプリングを示すグラフ" border="false":::

参考までに、次に示すのは元のタイムシリーズです (ダウンサンプリング前)。
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| render timechart with(xcolumn=TimeStamp, ycolumns=num)
```

:::image type="content" source="images/series-downsample-fl/original-time-series.png" alt-text="ダウンサンプリング前の元のタイムシリーズを示すグラフ" border="false":::

