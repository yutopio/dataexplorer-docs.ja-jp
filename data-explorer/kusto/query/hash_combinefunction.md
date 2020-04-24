---
title: hash_combine() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの hash_combine() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d0c6375436df298a97c03ec2f06f7cd492d59d7f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514265"
---
# <a name="hash_combine"></a>hash_combine()

複数のハッシュのハッシュ値を結合します。

**構文**

`hash_combine(`*h1* `,` *h2* [`,` *h3* ..]`)`

**引数**

* *h1*: 最初のハッシュ値を表す Long 値。
* *h2*: 2 番目のハッシュ値を表す Long 値。
* *hN*: N 番目のハッシュ値を表す long 値。

**戻り値**

指定されたスカラーの結合されたハッシュ値。

**使用例**

```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|組み合わせる|
|---|---|---|---|---|
|こんにちは|World|753694413698530628|1846988464401551951|-1440138333540407281|