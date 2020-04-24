---
title: url_decode() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで url_decode() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3ffa5052a2fc30387be118683ec1df6f34f7346f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505153"
---
# <a name="url_decode"></a>url_decode()

この関数は、エンコードされた URL を a から通常の URL 表現に変換します。 

URL のデコードとエンコードの詳細については、 こちらをご[覧ください](https://en.wikipedia.org/wiki/Percent-encoding)。

**構文**

`url_decode(`*エンコードされた URL*`)`

**引数**

* *エンコードされた URL*: エンコードされた URL (文字列)。  

**戻り値**

通常の表現での URL (文字列)。

**使用例**

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|original|デコード|
|---|---|
|を%3a%2f%2fwww.bing.com%2f|https://www.bing.com/|



 