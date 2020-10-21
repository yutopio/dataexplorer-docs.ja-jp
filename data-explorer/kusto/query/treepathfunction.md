---
title: treepath ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの treepath () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 91e11e6d45f82e6e27ea2da5ca1da945f190b69f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252006"
---
# <a name="treepath"></a>treepath()

動的オブジェクトのリーフを識別するパス式をすべて列挙します。

`treepath(`*動的オブジェクト*`)`

## <a name="returns"></a>戻り値

パス式の配列。

## <a name="examples"></a>例

|Expression|評価結果|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|