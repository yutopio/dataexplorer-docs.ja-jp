---
title: strlen() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで strlen() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33bb1ea56ebc03dc7357264bcdf987c191478cbb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506870"
---
# <a name="strlen"></a>strlen()

入力文字列の長さを文字数で返します。

**構文**

`strlen(`*ソース*`)`

**引数**

* *source*: 文字列の長さを測定するソース文字列。

**戻り値**

入力文字列の長さを文字数で返します。

**メモ**

文字列内の各 Unicode 文字は`1`、サロゲートを含む と等しくなります。
(例: 漢字は、UTF-8 エンコードで複数の値が必要であるにもかかわらず、1 回カウントされます)。


**使用例**

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