---
title: sign() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの sign() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86a57ed2fb7d43daf300731fe48233eca318bded
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507567"
---
# <a name="sign"></a>sign()

数式の符号

**構文**

`sign(`*X*`)`

**引数**

* *x*: 実数。

**戻り値**

* 指定した式の正符号 (+1)、ゼロ (0)、または負符号 (-1)。 

**使用例**

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|