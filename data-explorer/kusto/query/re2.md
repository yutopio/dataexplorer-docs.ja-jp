---
title: 正規表現 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の正規表現について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513082"
---
# <a name="re2-syntax"></a>RE2 構文

RE2 正規表現の構文は、Kusto で使用される正規表現ライブラリ (re2) の構文を記述します。
Kusto には、正規表現を使用して文字列の照合、選択、および抽出を実行する関数がいくつかあります。

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [parse 演算子](parseoperator.md)
- [replace()](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

Kusto でサポートされる正規表現の構文は、[re2 ライブラリ](https://github.com/google/re2/wiki/Syntax)の構文です。 これらの式は、Kusto では `string` リテラルとして エンコードする必要があり、すべての Kusto の文字列の引用符規則が適用されます。 たとえば、正規表現 `\A` は、行の先頭と一致し、Kusto では文字列リテラル `"\\A"` として指定されます ("追加の" バックスラッシュ (`\`) 文字に注意してください)。
