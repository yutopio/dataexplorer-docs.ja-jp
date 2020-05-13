---
title: プロジェクト名の変更演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのプロジェクト名の変更演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68581cfe4b3828823ced7d4704eb08df5d2aefa7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373170"
---
# <a name="project-rename-operator"></a>project-rename 演算子

結果の出力で列の名前を変更します。

```kusto
T | project-rename new_column_name = column_name
```

**構文**

*T* `| project-rename` *newcolumnname*  =  *existingcolumnname* [ `,` ...]

**引数**

* *T*: 入力テーブル。
* *Newcolumnname:* 列の新しい名前です。 
* *Existingcolumnname:* 列の既存の名前。 

**戻り値**

既存のテーブルと同じ順序で列を持ち、列の名前が変更されたテーブル。


**使用例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
