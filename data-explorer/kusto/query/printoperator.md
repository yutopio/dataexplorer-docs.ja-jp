---
title: print 操作-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの print 操作について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: 9a5a780a6f7bdf277566d1c0421c5ca2a3a93602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346067"
---
# <a name="print-operator"></a>print 演算子

1つ以上のスカラー式を使用して単一行を出力します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>構文

`print`[*ColumnName* `=` ]*ScalarExpression* [', '...]

## <a name="arguments"></a>引数

* *ColumnName*: 出力の単数形の列に割り当てるオプション名。
* *ScalarExpression*: 評価するスカラー式。

## <a name="returns"></a>戻り値

評価された*ScalarExpression*の値が1つのセルに含まれる単一列の単一行テーブル。

## <a name="examples"></a>例

演算子は、 `print` 1 つまたは複数のスカラー式を評価し、結果の値から単一行テーブルを作成するための簡単な方法として役立ちます。
次に例を示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
