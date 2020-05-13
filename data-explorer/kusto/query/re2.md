---
title: 正規表現-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの正規表現について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 3bbd14031adbfee3b5fac07194f5ff879ff33693
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373074"
---
# <a name="regular-expressions"></a>正規表現

RE2 正規表現の構文 Kusto (RE2) で使用される正規表現ライブラリの構文について説明します。
正規表現を使用して文字列の一致、選択、抽出を実行する Kusto の関数がいくつかあります。

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse 演算子](parseoperator.md)
- [replace ()](replacefunction.md)
- [trim ()](trimfunction.md)
- [trimend ()](trimendfunction.md)
- [trimstart ()](trimstartfunction.md)

Kusto でサポートされている正規表現の構文は、 [re2 ライブラリ](https://github.com/google/re2/wiki/Syntax)のものです。 これらの式は、リテラルとして Kusto にエンコードする必要があり `string` 、すべての kusto の文字列の引用符規則が適用されます。 たとえば、正規表現は、 `\A` 行の先頭に一致し、Kusto では文字列リテラルとして指定され `"\\A"` ます ("extra" 円記号 () 文字に注意して `\` ください)。
