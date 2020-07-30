---
title: todynamic ()、todynamic ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの todynamic ()、todynamic () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e11a4d450275fb4d596bd9618c20ef6cefcb0531
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350735"
---
# <a name="todynamic-toobject"></a>todynamic()、toobject()

を `string` [JSON 値](https://json.org/)として解釈し、値をとして返し [`dynamic`](./scalar-data-types/dynamic.md) ます。 

JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は、 [extractjson () 関数](./extractjsonfunction.md)を使用することをお勧めします。

[Parse_json ()](./parsejsonfunction.md)関数への別名。

## <a name="syntax"></a>構文

`todynamic(`*json* `)` 
 json `toobject(`*json*`)`

## <a name="arguments"></a>引数

* *json*: json ドキュメント。

## <a name="returns"></a>戻り値

*json* によって指定された、`dynamic` 型のオブジェクト。

*注*: 可能な場合は、[動的 ()](./scalar-data-types/dynamic.md)を使用することをお勧めします。