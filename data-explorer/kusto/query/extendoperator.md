---
title: extend 演算子 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の extend 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7ead6313128b99357dd61f18f55c5d5543943a05
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513354"
---
# <a name="extend-operator"></a>extend 演算子

計算列を作成し、それらを結果セットに追加します。

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>構文

*T* `| extend` [*ColumnName* | `(`*ColumnName*[`,` ...]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>引数

* *T*: 入力の表形式の結果セット。
* *ColumnName:* 省略可能。 追加または更新する列の名前。 省略した場合、名前が生成されます。 "*式*" によって複数の列が返される場合は、かっこで囲んで列名のリストを指定できます。 この場合、"*式*" の出力列には指定した名前が設定され、それ以外に出力列がある場合は削除されます。 列名のリストを指定しないと、"*式*" のすべての出力列に名前が生成されて、出力に追加されます。
* *Expression:* 入力の列に対する計算。

## <a name="returns"></a>戻り値

入力の表形式の結果セットのコピー。次のようなものです。
1. 入力に既に存在する `extend` を指定された列名は削除され、新しい計算値として追加されます。
2. 入力に存在しない `extend` を指定された列名は、新しい計算値として追加されます。

**ヒント**

* `extend` 演算子を使用すると、入力結果セットに新しい列が追加されます。インデックスは付けられて **いません**。 ほとんどの場合、新しい列が、インデックスを持つ既存のテーブル列とまったく同じに設定されている場合、Kusto で既存のインデックスを自動的に使用できます。 ただし、一部の複雑なシナリオでは、この反映は行われません。 そのような場合、列の名前を変更することが目的である場合は、代わりに [`project-rename` 演算子](projectrenameoperator.md)を使用します。

## <a name="example"></a>例

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

[series_stats](series-statsfunction.md) 関数を使用すると、複数の列を取得することができます。