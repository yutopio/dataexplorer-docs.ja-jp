---
title: hash_many() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの hash_many() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: e98f9d1d956d15cd7a61e7873f9b1dd34c6ae288
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514180"
---
# <a name="hash_many"></a>hash_many()

複数の値のハッシュ値を組み合わせた値を返します。

**構文**

`hash_many(`*s1* `,` *s2* [`,` *s3* .]]`)`

**引数**

* *s1*、 *s2*、.,sN : 一緒にハッシュされる入力値。 *sN*

**戻り値**

指定されたスカラーの結合されたハッシュ値。

**使用例**

```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|組み合わせる|
|---|---|---|
|こんにちは|World|-1440138333540407281|