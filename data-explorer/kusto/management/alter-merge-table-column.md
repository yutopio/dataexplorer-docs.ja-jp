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
ms.openlocfilehash: 9f71a46e6c747b7f9d9f6a3ba2d2f8a308635128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967215"
---
# <a name="alter-merge-table-column-docstrings"></a>.alter-merge table column-docstring

`docstring`指定されたテーブルの1つ以上の列のプロパティを設定します。 明示的に設定されていない列には、このプロパティの既存の値が**保持**されます (存在する場合)。

Alter table docstring については、[以下](#alter-table-column-docstrings)を参照してください。

**構文**

`.alter-merge``table` *TableName* `column-docstring` TableName `(`*Col1* `:`*Docstring1* [ `,` *Col2* `:` *Docstring2*]...`)`

**例** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>alter table 列-docstrings

`docstring`指定されたテーブルの1つ以上の列のプロパティを設定します。 明示的に設定されていない列の場合、このプロパティは**削除**されます。

**構文**

`.alter``table` *TableName* `column-docstring` TableName `(`*Col1* `:`*Docstring1* [ `,` *Col2* `:` *Docstring2*]...`)`

**例** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
