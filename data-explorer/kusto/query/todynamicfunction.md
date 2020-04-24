---
title: todynamic() を、オブジェクトにする() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの todynamic() 、toobject() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 138b0f978df699817c5dc5c14bafc4c06a95afc7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506156"
---
# <a name="todynamic-toobject"></a>を、オブジェクトにする()

を`string` [JSON 値](https://json.org/)として解釈し、値を[`dynamic`](./scalar-data-types/dynamic.md)として返します。 

JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は[、extractjson() 関数](./extractjsonfunction.md)を使用するよりも優れています。

[parse_json()](./parsejsonfunction.md)関数のエイリアス。

**構文**

`todynamic(`*json*`)`json`toobject(`*json* 
json`)`

**引数**

* *json*: JSON ドキュメント。

**戻り値**

*json* によって指定された、`dynamic` 型のオブジェクト。

*注意*: 可能な場合[は dynamic()](./scalar-data-types/dynamic.md)を使用することをお好みで指定します。