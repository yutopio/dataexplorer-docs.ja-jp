---
title: トストリング() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで tostring() を説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51aaf90b60653a648457dc00200168aec7fbefd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505884"
---
# <a name="tostring"></a>tostring()

入力を文字列形式に変換します。

```kusto
tostring(123) == "123"
```

**構文**

`tostring(`*Expr*`)`

**引数**

* *Expr*: 文字列に変換される式。 

**戻り値**

*Expr*値が null 以外の場合、結果は*Expr*の文字列表現になります。
*Expr*値が null の場合、結果は空の文字列になります。
 