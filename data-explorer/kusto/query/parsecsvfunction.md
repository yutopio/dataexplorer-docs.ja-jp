---
title: parse_csv() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでparse_csv() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6645fd0dcc7062a4afb60fb34ade1c519af27294
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663670"
---
# <a name="parse_csv"></a>parse_csv()

コンマ区切り値の単一レコードを表す指定された文字列を分割し、これらの値を持つ文字列配列を返します。

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**構文**

`parse_csv(`*ソース*`)`

**引数**

* *source*: コンマ区切り値の単一レコードを表すソース文字列。

**戻り値**

分割された値を含む文字列配列。

**メモ**

埋め込み改行、カンマ、引用符は二重引用符 (''') を使用してエスケープできます。 この関数は、1 行に複数のレコードをサポートしません (最初のレコードのみが取られます)。

**使用例**

```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|結果|
|---|
|[<br>  "aa"、<br>  "b,b,b",<br>  "cc",<br>  「エスケープ引用符:\"タイトル\"」、<br>  "ライン1\nline2"<br>]|

複数のレコードを含む CSV ペイロード:

```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "レコード1"、<br>  "a"、<br>  "b"、<br>  "c"<br>]|