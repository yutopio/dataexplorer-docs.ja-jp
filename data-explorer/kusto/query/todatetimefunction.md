---
title: todatetime() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの todatetime() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe3852195b1a79ab5c86176698099ed6e7ff7af
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506343"
---
# <a name="todatetime"></a>todatetime()

入力を[日付時刻](./scalar-data-types/datetime.md)スカラーに変換します。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**構文**

`todatetime(`*Expr*`)`

**引数**

* *Expr*:[日付時刻](./scalar-data-types/datetime.md)に変換される式。 

**戻り値**

変換が成功すると、結果は[日時](./scalar-data-types/datetime.md)値になります。
変換が成功しなかった場合、結果は null になります。
 
*注意*: 可能な場合[は、datetime()](./scalar-data-types/datetime.md)を使用することをお好みで指定してください。