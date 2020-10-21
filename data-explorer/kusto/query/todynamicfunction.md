---
title: todynamic ()、todynamic ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの todynamic ()、todynamic () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c92ff96b3ae6a0369de1c8fa715e64499fb051b7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250478"
---
# <a name="todynamic-toobject"></a>todynamic()、toobject()

を `string` [JSON 値](https://json.org/) として解釈し、値をとして返し [`dynamic`](./scalar-data-types/dynamic.md) ます。 

JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は、 [extractjson () 関数](./extractjsonfunction.md) を使用することをお勧めします。

[Parse_json ()](./parsejsonfunction.md)関数への別名。

> [!NOTE]
> 可能な場合は、 [動的 ()](./scalar-data-types/dynamic.md) を使用することをお勧めします。

## <a name="syntax"></a>構文

`todynamic(`*json* `)` 
 json `toobject(`*json*`)`

## <a name="arguments"></a>引数

* *json*: json ドキュメント。

## <a name="returns"></a>戻り値

*json* によって指定された、`dynamic` 型のオブジェクト。
