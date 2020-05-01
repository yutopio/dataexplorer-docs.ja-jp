---
title: alter-merge table docstrings-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの alter-merge テーブル docstrings について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7dd36181be1140d3960369b1c8a5284ed55e48f5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616492"
---
# <a name="alter-merge-table-column-docstrings"></a>alter-merge table 列-docstrings

指定さ`docstring`れたテーブルの1つ以上の列のプロパティを設定します。 明示的に設定されていない列には、このプロパティの既存の値が**保持**されます (存在する場合)。

Alter table docstring については、[以下](#alter-table-column-docstrings)を参照してください。

**構文**

`.alter-merge``table` *TableName* `:` *Docstring1* *Col1* `:` *Docstring2*Col1 Docstring1 [`,` Col2 Docstring2]... *Col2* `column-docstring` `(``)`

**例** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>alter table 列-docstrings

指定さ`docstring`れたテーブルの1つ以上の列のプロパティを設定します。 明示的に設定されていない列の場合、このプロパティは**削除**されます。

**構文**

`.alter``table` *TableName* `:` *Docstring1* *Col1* `:` *Docstring2*Col1 Docstring1 [`,` Col2 Docstring2]... *Col2* `column-docstring` `(``)`

**例** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
