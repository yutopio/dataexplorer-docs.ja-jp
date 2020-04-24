---
title: isnull() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで isnull() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e26dca661ceac1ad209358b24b3f8d497a5c3049
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513415"
---
# <a name="isnull"></a>isnull()

その唯一の引数を`bool`評価し、引数が null 値に評価されるかどうかを示す値を返します。

```kusto
isnull(parse_json("")) == true
```

**構文**

`isnull(`*Expr*`)`

**戻り値**

値が null かどうかに応じて、True または false を返します。

**メモ**

* `string`値を NULL にすることはできません。 型[の値が空かどうかを判断するには、isempty](./isemptyfunction.md)を使用します`string`。

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

**例**

```kusto
T | where isnull(PossiblyNull) | count
```