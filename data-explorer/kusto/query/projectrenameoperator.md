---
title: プロジェクト名の変更演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのプロジェクト名変更演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f7fbf2efbfd3ed1dfac9129ec21f5a13c51481c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510865"
---
# <a name="project-rename-operator"></a>project-rename 演算子

結果出力の列の名前を変更します。

```kusto
T | project-rename new_column_name = column_name
```

**構文**

*T* `| project-rename` *新しい列名* = *既存の列名*[`,` .]

**引数**

* *T*: 入力テーブル。
* *新しい列名:* 列の新しい名前。 
* *既存の列名:* 列の既存の名前。 

**戻り値**

既存のテーブルと同じ順序で列を持つテーブルで、列の名前が変更されています。


**使用例**

```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|