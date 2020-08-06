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
ms.openlocfilehash: acd1b5328150e61bc81930f94b8ea9e8025e1ebb
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804085"
---
# <a name="todynamic-toobject"></a>todynamic()、toobject()

を `string` [JSON 値](https://json.org/)として解釈し、値をとして返し [`dynamic`](./scalar-data-types/dynamic.md) ます。 

JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は、 [extractjson () 関数](./extractjsonfunction.md)を使用することをお勧めします。

[Parse_json ()](./parsejsonfunction.md)関数への別名。

> [!NOTE]
> 可能な場合は、[動的 ()](./scalar-data-types/dynamic.md)を使用することをお勧めします。

## <a name="syntax"></a>構文

`todynamic(`*json* `)` 
 json `toobject(`*json*`)`

## <a name="arguments"></a>引数

* *json*: json ドキュメント。

## <a name="returns"></a>戻り値

*json* によって指定された、`dynamic` 型のオブジェクト。
