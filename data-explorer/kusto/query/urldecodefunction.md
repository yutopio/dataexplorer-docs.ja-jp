---
title: url_decode ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの url_decode () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b58fea5d367cf31b495b23a09bc0a0dcb6bb95c6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251863"
---
# <a name="url_decode"></a>url_decode()

関数は、エンコードされた URL を標準の URL 表現に変換します。 

URL のデコードとエンコードの詳細については、 [こちら](https://en.wikipedia.org/wiki/Percent-encoding)を参照してください。

## <a name="syntax"></a>構文

`url_decode(`*エンコードされた url*`)`

## <a name="arguments"></a>引数

* *エンコード*された url: エンコードされた url (string)。  

## <a name="returns"></a>戻り値

正規表現の URL (文字列)。

## <a name="examples"></a>例

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|original|デコード|
|---|---|
|https %3 a %2 f %2 f www. bing .com% 2f|https://www.bing.com/|



 