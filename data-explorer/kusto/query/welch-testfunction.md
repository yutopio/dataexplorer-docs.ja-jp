---
title: welch_test ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの welch_test () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063e0db08b3566ed69957dda675082d8ea6942cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253178"
---
# <a name="welch_test"></a>welch_test()

の p_value を計算し [ます。](https://en.wikipedia.org/wiki/Welch%27s_t-test)

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

## <a name="syntax"></a>構文

`welch_test(`*mean1* `, `*variance1* `, `*count1* `, `*mean2* `, `*variance2* `, `*count2*`)`

## <a name="arguments"></a>引数

* *mean1*: 最初の系列の平均 (平均) 値を表す式
* *variance1*: 最初の系列の分散値を表す式
* *count1*: 最初の系列の値の数を表す式
* *mean2*: 2 番目の系列の平均 (平均) 値を表す式
* *variance2*: 2 番目の系列の分散値を表す式
* *count2*: 2 番目の系列の値の数を表す式

## <a name="returns"></a>戻り値

[Wikipedia](https://en.wikipedia.org/wiki/Welch%27s_t-test)から:

Statistics では、次の2つのサンプルの場所テストを使用して、2つの母集団が等しいという仮説をテストします。 この t 検定は、スチューデントの t 検定を適合させるものであり、2つのサンプルの差異が等しくなく、サンプルサイズが等しくない場合にも信頼性が高くなります。 これらのテストは、"対になっていない" または "独立したサンプル" と呼ばれることがよくあります。 テストは通常、比較対象の2つのサンプルの基になる統計単位が重複していない場合に適用されます。 この t 検定は、スチューデントの t 検定よりも一般的ではありません。閲覧者にとってはあまりなじみがありません。 このテストは、"" "という名前の" "は、" ' 等分散の等しくない差異 "と呼ばれることもあります。
