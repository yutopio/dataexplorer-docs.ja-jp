---
title: string_size ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの string_size () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4832d00703ab9e6d1478af5b3f45ec294dfb79ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350912"
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