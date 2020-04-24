---
title: welch_test() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで welch_test() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9f4b3d86bf3d679fd4fdd320b394956c71d3e97
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504490"
---
# <a name="welch_test"></a>welch_test()

[ウェルチテスト関数のp_valueを計算します。](https://en.wikipedia.org/wiki/Welch%27s_t-test)

```kusto
// s1, s2 values are from https://en.wikipedia.org/wiki/Welch%27s_t-test
print
    s1 = dynamic([27.5, 21.0, 19.0, 23.6, 17.0, 17.9, 16.9, 20.1, 21.9, 22.6, 23.1, 19.6, 19.0, 21.7, 21.4]),
    s2 = dynamic([27.1, 22.0, 20.8, 23.4, 23.4, 23.5, 25.8, 22.0, 24.8, 20.2, 21.9, 22.1, 22.9, 20.5, 24.4])
| mv-expand s1 to typeof(double), s2 to typeof(double)
| summarize m1=avg(s1), v1=variance(s1), c1=count(), m2=avg(s2), v2=variance(s2), c2=count()
| extend pValue=welch_test(m1,v1,c1,m2,v2,c2)

// pValue = 0.021
```

**構文**

`welch_test(`*平均1*`, `*分散1*`, `*個数1*`, `*平均2*`, `*分散2*`, `*カウント2*`)`

**引数**

* *平均1*: 第1系列の平均値を表す式
* *variance1*: 第 1 系列の分散値を表す式
* *count1*: 第 1 系列の値の数を表す式
* *平均2*: 第2系列の平均値を表す式
* *variance2*: 第 2 系列の分散値を表す式
* *count2*: 第 2 系列の値の数を表す式

**戻り値**

[ウィキペディア](https://en.wikipedia.org/wiki/Welch%27s_t-test)から :

統計では、ウェルチのt検定、または不均一な分散t検定は、2つの集団が等しい平均を有するという仮説をテストするために使用される2サンプルの位置テストです。 Welchのt検定は、学生のt検定の適応であり、つまり、Studentのt検定の助けを借りて導き出され、2つのサンプルが不均一な分散と不均一なサンプルサイズを有する場合、より信頼性が高い。 これらの検定は、比較対象の 2 つのサンプルの基になる統計単位が重複していない場合に適用されるため、しばしば「非対」または「独立サンプル」t検定と呼ばれます。 Welchのt検定はStudentのt-testよりも人気がなく、読者にはあまり馴染みがないかもしれないことを考えると、より有益な名前は簡潔さのために「ウェルチの不平等な分散t-test」または「不平等な分散t検定」です。