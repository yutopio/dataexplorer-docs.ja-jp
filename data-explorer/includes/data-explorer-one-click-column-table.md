---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 06/11/2020
ms.author: orspodek
ms.openlocfilehash: 3d366a824ecfdf89c56b1049b70df664573cd7e0
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "97371664"
---
テーブルに加えることができる変更は、次のパラメーターによって異なります。
* **テーブル** の種類が新規かまたは既存か
* **マッピング** の種類が新規かまたは既存か

テーブルの種類です。 | マッピングの種類 | 使用可能な調整|
|---|---|---|
|新しいテーブル   | 新しいマッピング |データ型の変更、列名の変更、列の削除、昇順で並べ替え、降順で並べ替え  |
|既存のテーブル  | 新しいマッピング | 新しい列 (その後データ型の変更、名前の変更、および更新が可能) <br> 新しい列、昇順で並べ替え、降順で並べ替え  |
| | 既存のマッピング | 昇順で並べ替え、降順で並べ替え

> [!NOTE]
> 新しい列を追加するとき、または列を更新するときに、マッピング変換を変更できます。 詳細については、[マッピング変換](../ingest-data-one-click.md#mapping-transformations)に関するページを参照してください。