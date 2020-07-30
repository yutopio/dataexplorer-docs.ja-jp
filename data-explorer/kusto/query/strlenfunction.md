---
title: strlen ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの strlen () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d28eae6852faedf2c6071164d8f80f9c3567602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350895"
---
# <a name="strlen"></a>strlen()

入力文字列の長さを文字数で返します。

## <a name="syntax"></a>構文

`strlen(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: 文字列の長さに対して測定されるソース文字列。

## <a name="returns"></a>戻り値

入力文字列の長さを文字数で返します。

**メモ**

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