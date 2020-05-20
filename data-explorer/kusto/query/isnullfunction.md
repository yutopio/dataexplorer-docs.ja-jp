---
title: isnull ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの isnull () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4c57c7aba2bff2dfaecfa72b20ab76cc84ed17d6
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550590"
---
# <a name="isnull"></a>isnull()

唯一の引数を評価し、 `bool` 引数が null 値に評価されるかどうかを示す値を返します。

```kusto
isnull(parse_json("")) == true
```

**構文**

`isnull(`*With*`)`

**戻り値**

値が null かどうかによって、True または false になります。

**メモ**

* `string`値を null にすることはできません。 [Isempty](./isemptyfunction.md)を使用して、型の値が空であるかどうかを確認し `string` ます。

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