---
title: toguid() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで toguid() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb1df5fe91b75e5d3b7d1a9f40b8b3079cac9944
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506139"
---
# <a name="toguid"></a>toguid()

入力を表現に[`guid`](./scalar-data-types/guid.md)変換します。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

**構文**

`toguid(`*Expr*`)`

**引数**

* *Expr*: スカラーに変換[`guid`](./scalar-data-types/guid.md)される式。 

**戻り値**

変換が成功すると、結果は[`guid`](./scalar-data-types/guid.md)スカラーになります。
変換が成功しなかった場合は、 が`null`返されます。

*注意*: 可能な場合[は、guid() を](./scalar-data-types/guid.md)使用することをお好みで指定します。