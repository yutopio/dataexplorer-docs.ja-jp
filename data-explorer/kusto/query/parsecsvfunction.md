---
title: parse_csv ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの parse_csv () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eddf893d3c0d8096f57a3f624ddedf753fe35f91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346356"
---
# <a name="parse_csv"></a>parse_csv()

コンマ区切り値の1つのレコードを表す特定の文字列を分割し、これらの値を持つ文字列配列を返します。

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>構文

`parse_csv(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: コンマ区切り値の1つのレコードを表すソース文字列。

## <a name="returns"></a>戻り値

分割された値を格納している文字列配列。

**メモ**

埋め込み行フィード、コンマ、および引用符は、二重引用符 (' "') を使用してエスケープできます。 この関数では、行ごとに複数のレコードがサポートされません (最初のレコードのみが取得されます)。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|結果|
|---|
|[<br>  "aa"、<br>  "b, b, b",<br>  "cc"、<br>  "引用符のエスケープ: \" タイトル \" ",<br>  "line1\nline2"<br>]|

複数のレコードを含む CSV ペイロード:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1",<br>  "a"、<br>  "b",<br>  "c"<br>]|
