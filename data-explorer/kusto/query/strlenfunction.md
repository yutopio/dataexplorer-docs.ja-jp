---
title: strlen ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの strlen () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4256218669685ec12a939156021803105e5ddfc6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248411"
---
# <a name="strlen"></a>strlen()

入力文字列の長さを文字数で返します。

## <a name="syntax"></a>構文

`strlen(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: 文字列の長さに対して測定されるソース文字列。

## <a name="returns"></a>戻り値

入力文字列の長さを文字数で返します。

**ノート**

文字列内の各 Unicode 文字は、サロゲートを含むと同じです `1` 。
(たとえば、UTF-8 エンコードでは、複数の値が必要であるという事実にかかわらず、中国語の文字は1回カウントされます)。


## <a name="examples"></a>例

```kusto
print length = strlen("hello")
```

|length|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|length|
|---|
|5|