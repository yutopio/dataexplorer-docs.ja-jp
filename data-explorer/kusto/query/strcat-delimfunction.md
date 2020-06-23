---
title: strcat_delim ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの strcat_delim () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6a78a5abb92aa93fe8b1ae15ea8968f71bde07c
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264557"
---
# <a name="strcat_delim"></a>strcat_delim()

最初の引数として指定された区切り記号を使用して、2 ~ 64 の引数を連結します。

 * 引数が文字列型でない場合は、文字列に強制的に変換されます。

**構文**

`strcat_delim(`*delimiter*、*引数 1*、*引数 2*[、 *argumentn*]`)`

**引数**

* *delimiter*: 文字列式。区切り記号として使用されます。
* *引数 1* ...*argumentn*: 連結する式。

**戻り値**

引数。*区切り記号*付きの1つの文字列に連結されます。

**使用例**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
