---
title: 正規表現 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの正規表現について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0bc5dc6705d6cada446716f2f9d84618322ecd76
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510525"
---
# <a name="regular-expressions"></a>正規表現

RE2 正規表現構文は Kusto (re2) で使用される正規表現ライブラリの構文を記述します。
Kusto には、正規表現を使用して文字列の一致、選択、抽出を実行するいくつかの関数があります。

- [カウントフ()](countoffunction.md)
- [抽出()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse 演算子](parseoperator.md)
- [置換()](replacefunction.md)
- [トリム()](trimfunction.md)
- [トリムエンド()](trimendfunction.md)
- [トリムスタート()](trimstartfunction.md)

Kusto でサポートされている正規表現の構文は[re2 ライブラリ](https://github.com/google/re2/wiki/Syntax)の構文で、以下で詳しく説明します。 これらの式は、リテラルとして`string`Kusto でエンコードする必要があり、Kusto の文字列引用規則がすべて適用されることに注意してください。 たとえば、正規表現`\A`は行の先頭と一致し (下の表を参照)、文字列リテラル`"\\A"`として Kusto で指定されます ("余分な"`\`円記号 ( ) 文字に注意してください)。

