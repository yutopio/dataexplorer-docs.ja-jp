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
ms.openlocfilehash: 9bc000ffa57d906c3e65e54e9daac5431f8dc276
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346016"
---
# <a name="project-rename-operator"></a>project-rename 演算子

結果の出力で列の名前を変更します。

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>構文

*T* `| project-rename` *newcolumnname*  =  *existingcolumnname* [ `,` ...]

## <a name="arguments"></a>引数

* *T*: 入力テーブル。
* *Newcolumnname:* 列の新しい名前です。 
* *Existingcolumnname:* 列の既存の名前。 

## <a name="returns"></a>戻り値

既存のテーブルと同じ順序で列を持ち、列の名前が変更されたテーブル。


## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
