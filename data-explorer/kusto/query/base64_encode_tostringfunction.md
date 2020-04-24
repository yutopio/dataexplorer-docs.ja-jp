---
title: base64_encode_tostring() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで base64_encode_tostring() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a80b0aa0e3f7e5f330da87f93bbad44e587bcdaf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518056"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

文字列を base64 文字列としてエンコードします。

**構文**

`base64_encode_tostring(`*文字列*`)`

**引数**

* *文字列*: base64 文字列としてエンコードされる入力文字列。

**戻り値**

base64 文字列としてエンコードされた文字列を返します。

* base64 文字列を UTF-8 文字列にデコードする場合は[、base64_decode_tostring() を](base64_decode_tostringfunction.md)参照してください。
* base64 文字列を長い値の配列にデコードする場合は[、base64_decode_toarray() を](base64_decode_toarrayfunction.md)参照してください。


**例**

```kusto
print Quine=base64_encode_tostring("Kusto")
```

|クワイン   |
|--------|
|S3VzdG8=|
