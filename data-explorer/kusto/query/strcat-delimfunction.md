---
title: strcat_delim() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでstrcat_delim() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f944af741cd5f655c2c9b090ddebc6cc35a47766
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506938"
---
# <a name="strcat_delim"></a>strcat_delim()

2 ~ 64 個の引数を区切り文字で連結し、最初の引数として指定します。

 * 引数が文字列型でない場合、引数は強制的に文字列に変換されます。

**構文**

`strcat_delim(`*区切り文字*、*引数 1*、*引数 2* [,*引数N]*`)`

**引数**

* *区切り文字*: 区切り文字として使用される文字列式。
* *引数1..**引数N* : 連結する式。

**戻り値**

引数は、*区切り文字*を持つ単一の文字列に連結されます。

**使用例**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|