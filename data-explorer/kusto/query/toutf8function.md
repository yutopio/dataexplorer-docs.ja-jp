---
title: to_utf8 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの to_utf8 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c47c37dbbde7bd2276f1b5788dc6eb7b062f39a3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251275"
---
# <a name="to_utf8"></a>to_utf8()

入力文字列の unicode 文字の動的配列を返します (make_string の逆の操作)。

## <a name="syntax"></a>構文

`to_utf8(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: 変換するソース文字列。

## <a name="returns"></a>戻り値

この関数に渡された文字列を構成する unicode 文字の動的配列を返します。
を参照 [`make_string()`](makestringfunction.md) )

## <a name="examples"></a>例

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|→|
|---|
|[9382、9392、9390、9391、9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|→|
|---|
|[1511、1493、1505、1496、1493、32、45、32、75、117、115、116、111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
