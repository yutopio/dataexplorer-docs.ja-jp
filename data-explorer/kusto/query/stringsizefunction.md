---
title: string_size() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの string_size() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7886e7b8fd5039c9b197ae435bce4f40b93e5038
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506887"
---
# <a name="string_size"></a>string_size()

入力文字列のサイズ (バイト単位) を返します。

**構文**

`string_size(`*ソース*`)`

**引数**

* *source*: 文字列サイズを測定するソース文字列。

**戻り値**

入力文字列の長さ (バイト単位) を返します。

**使用例**

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