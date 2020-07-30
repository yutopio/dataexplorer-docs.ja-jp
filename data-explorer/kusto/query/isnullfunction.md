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
ms.openlocfilehash: d1bea6260ca86e6ca47be843a6acc4fb43a037b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347172"
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

**メモ**

* `string`値を null にすることはできません。 [Isempty](./isemptyfunction.md)を使用して、型の値が空であるかどうかを確認し `string` ます。

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