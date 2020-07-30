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
ms.openlocfilehash: 2568196dc20042e95521ed0818bd625f3394599b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342565"
---
# <a name="strcat_delim"></a>strcat_delim()

最初の引数として指定された区切り記号を使用して、2 ~ 64 の引数を連結します。

 * 引数が文字列型でない場合は、文字列に強制的に変換されます。

## <a name="syntax"></a>構文

`strcat_delim(`*delimiter*、*引数 1*、*引数 2*[、 *argumentn*]`)`

## <a name="arguments"></a>引数

* *delimiter*: 文字列式。区切り記号として使用されます。
* *引数 1* ...*argumentn*: 連結する式。

## <a name="returns"></a>戻り値

引数。*区切り記号*付きの1つの文字列に連結されます。

## <a name="examples"></a>例

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
