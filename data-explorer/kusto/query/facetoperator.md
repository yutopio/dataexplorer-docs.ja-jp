---
title: ファセット オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのファセット オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b6c87fc044e0f00f77e28e85d89757a69835164
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515319"
---
# <a name="facet-operator"></a>facet 演算子

指定された各列に対して 1 つずつ、テーブルのセットを返します。
各テーブルは、その列で取得される値のリストを指定します。
句を使用して、追加のテーブルを`with`作成できます。

**構文**

*T* `| facet by` *列名*[`, ` ..[`with (` *フィルターパイプ*`)`

**引数**

* *列名:* 出力テーブルとして集計される入力内の列の名前。
* *フィルターパイプ:* 出力の 1 つを生成するために入力テーブルに適用されるクエリ式。

**戻り値**

複数のテーブル: 句`with`用に 1 つ、列ごとに 1 つ。

**例**

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```