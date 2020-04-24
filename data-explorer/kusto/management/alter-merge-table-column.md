---
title: 変更マージ テーブルの列ドキュメント文字列 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの変更マージ テーブルの列ドキュメント文字列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75d298f35a215af5da443f673278e4a252c24cc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522391"
---
# <a name="alter-merge-table-column-docstrings"></a>変更マージ テーブルの列-docstrings

指定した`docstring`テーブルの 1 つ以上の列のプロパティを設定します。 明示的に設定されていない列は、このプロパティに既存の値がある場合は **、その**値を保持します。

テーブル列-docstring の変更については、[以下](#alter-table-column-docstrings)を参照してください。

**構文**

`.alter-merge``table`*テーブル*`column-docstring`名`(`*コル1* `:`*ドク文字列1* [`,`*コル2*`:`*ドキュメント文字列2..*`)`

**例** 

```
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>テーブルの列の文字列を変更する

指定した`docstring`テーブルの 1 つ以上の列のプロパティを設定します。 明示的に設定されていない列では、このプロパティは**削除されます**。

**構文**

`.alter``table`*テーブル*`column-docstring`名`(`*コル1* `:`*ドク文字列1* [`,`*コル2*`:`*ドキュメント文字列2..*`)`

**例** 

```
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```