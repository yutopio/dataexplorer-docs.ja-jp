---
title: string_size ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの string_size () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea06e7e41ebe48e09839109745f3fe7ba5a96e7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251473"
---
# <a name="string_size"></a>string_size()

入力文字列のサイズ (バイト単位) を返します。

## <a name="syntax"></a>構文

`string_size(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: 文字列サイズに対して測定されるソース文字列。

## <a name="returns"></a>戻り値

入力文字列の長さをバイト単位で返します。

## <a name="examples"></a>例

```kusto
print size = string_size("hello")
```

|size|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|size|
|---|
|15|