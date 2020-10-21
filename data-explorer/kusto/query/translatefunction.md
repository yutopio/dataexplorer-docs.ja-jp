---
title: translate ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの translate () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: 5d186172a0be1780347dbf89600c200c00687291
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252040"
---
# <a name="translate"></a>translate()

指定された文字列の文字セット (' searchList ') を別の文字セット (' replacementList ') に置換します。
関数は、' searchList ' 内の文字を検索し、' replacementList ' 内の対応する文字に置き換えます。

## <a name="syntax"></a>構文

`translate(`*Searchlist* `,`*replacementList* `,`*テキスト*`)`

## <a name="arguments"></a>引数

* *searchlist*: 置き換える必要がある文字の一覧
* *replacementList*: ' searchlist ' 内の文字を置き換える必要がある文字の一覧
* *text*: 検索する文字列

## <a name="returns"></a>戻り値

' replacementList ' のすべての ocurrences 文字を ' searchList ' 内の対応する文字に置き換えた後の*テキスト*

## <a name="examples"></a>例

|入力                                 |出力   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|