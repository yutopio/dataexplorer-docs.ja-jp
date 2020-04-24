---
title: url_encode_component() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの url_encode_component() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: bfdb40f362aa680a68bd8871769eecb5a2da19e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505068"
---
# <a name="url_encode_component"></a>url_encode_component()

この関数は、入力 URL の文字をインターネット上で転送できる形式に変換します。 

URL のエンコードとデコードの詳細については、 を[参照してください](https://en.wikipedia.org/wiki/Percent-encoding)。
[url_encode](./urlencodefunction.md)とは異なり、スペースを'+' ではなく「20%」としてエンコードします。

**構文**

`url_encode_component(`*Url*`)`

**引数**

* *url*: 入力 URL (文字列)  

**戻り値**

URL (文字列) をインターネット経由で転送できる形式に変換します。

**使用例**

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|original|エンコード|
|---|---|
|https://www.bing.com/hello単語/|https%3a%2f%2fwww.bing.com%2fhello%20ワード|


 