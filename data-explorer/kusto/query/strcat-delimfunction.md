---
title: strcat_delim ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの strcat_delim () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de34f4a2f6efb3af84b61f53da8d58cb60dea7ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243703"
---
# <a name="strcat_delim"></a>strcat_delim()

最初の引数として指定された区切り記号を使用して、2 ~ 64 の引数を連結します。

 * 引数が文字列型でない場合は、文字列に強制的に変換されます。

## <a name="syntax"></a>構文

`strcat_delim(`*delimiter*、 *引数 1*、 *引数 2*[、 *argumentn*]`)`

## <a name="arguments"></a>引数

* *delimiter*: 文字列式。区切り記号として使用されます。
* *引数 1* ... *argumentn*: 連結する式。

## <a name="returns"></a>戻り値

引数。 *区切り記号*付きの1つの文字列に連結されます。

## <a name="examples"></a>例

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
