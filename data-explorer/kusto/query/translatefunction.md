---
title: 翻訳() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの translate() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: f128ce31af7fc720ee81b7471d3d74616197b8d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505714"
---
# <a name="translate"></a>translate()

指定された文字列内の文字のセット ('検索リスト') を別の文字セット ('置換リスト') に置き換えます。
関数は '検索リスト' 内の文字を検索し、'置換リスト' 内の対応する文字に置き換えます。

**構文**

`translate(`*検索リスト*`,`*置換リストテキスト*`,`*text*`)`

**引数**

* *検索リスト*: 置換する必要がある文字のリスト
* *置換リスト*: '検索リスト' の文字を置き換える必要がある文字のリスト
* *text*: 検索する文字列

**戻り値**

'置換リスト' 内の文字のすべてのオカレンスを '検索リスト' の対応する文字に置き換えた後の*テキスト*

**使用例**

|入力                                 |出力   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|