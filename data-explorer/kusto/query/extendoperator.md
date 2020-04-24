---
title: 拡張オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの拡張オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b9b7bdb9488b0e9d1b72b3e0ab4782020c9b841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515591"
---
# <a name="extend-operator"></a>extend 演算子

計算列を作成し、結果セットに追加します。

```kusto
T | extend duration = endTime - startTime
```

**構文**

*T* `| extend` [*列名* | `(``,`*列名*] [.]] 式`,`[ .] *Expression* `)` `=`

**引数**

* *T*: 入力表形式の結果セット。
* *列名:* オプション。 追加または更新する列の名前。 省略すると、名前が生成されます。 *Expression*が複数の列を返す場合は、列名のリストをかっこで囲んで指定できます。 この場合 *、Expression*の出力列には指定された名前が付けられます。 列名のリストが指定されていない場合、生成された名前を持つ*すべての式*の出力列が出力に追加されます。
* *式:* 入力の列に対する計算。

**戻り値**

次のような入力表形式の結果セットのコピー。
1. 入力に既`extend`に存在する列名は削除され、新しい計算値として追加されます。
2. 入力に存在しない`extend`列名は、新しい計算値として追加されます。

**ヒント**

* 演算子`extend`は、インデックスを持**たない**入力結果セットに新しい列を追加します。 ほとんどの場合、新しい列がインデックスを持つ既存のテーブル列とまったく同じに設定されている場合、Kusto は既存のインデックスを自動的に使用できます。 ただし、複雑なシナリオでは、この伝播は行われません。 このような場合は、列の名前を変更する場合は、代わりに[`project-rename`演算子](projectrenameoperator.md)を使用します。

**例**

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

[series_stats](series-statsfunction.md)関数を使用して、複数の列を返すことができます。