---
title: isnull ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの isnull () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afaff2c00ca9136e113639deed886d039d21fda9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241527"
---
# <a name="isnull"></a>isnull()

唯一の引数を評価し、 `bool` 引数が null 値に評価されるかどうかを示す値を返します。

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>構文

`isnull(`*With*`)`

## <a name="returns"></a>戻り値

値が null かどうかによって、True または false になります。

**ノート**

* `string` 値を null にすることはできません。 [Isempty](./isemptyfunction.md)を使用して、型の値が空であるかどうかを確認し `string` ます。

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

## <a name="example"></a>例

```kusto
T | where isnull(PossiblyNull) | count
```