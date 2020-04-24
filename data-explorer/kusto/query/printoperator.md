---
title: 印刷オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの印刷オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: b751b07446a74ef17002abac26e4c881a2da757b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510967"
---
# <a name="print-operator"></a>print 演算子

1 つ以上のスカラー式を含む単一行を出力します。

```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**構文**

`print`[*列名*`=`]*スカラー表現*[','

**引数**

* *列名*: 出力の単数列に割り当てるオプション名。
* *スカラー式*: 評価するスカラー式。

**戻り値**

単一セルが評価された*ScalarExpression*の値を持つ単一列の単一行のテーブル。

**使用例**

この`print`演算子は、1 つ以上のスカラー式を評価し、結果の値から単一行テーブルを作成する簡単な方法として役立ちます。
次に例を示します。

```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```

```kusto
print banner=strcat("Hello", ", ", "World!")
```