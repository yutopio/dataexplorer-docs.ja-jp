---
title: プレビュープラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのプレビュープラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 455ff1d4d3a42c09a39673028405d51b7acd1f5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251639"
---
# <a name="preview-plugin"></a>preview プラグイン

入力レコードセットから指定された行数までのテーブルと、入力レコードセット内のレコードの合計数を返します。

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>構文

`T``|` `evaluate` `preview(`*Numberofrows*`)`

## <a name="returns"></a>戻り値

この `preview` プラグインは、次の2つの結果テーブルを返します。
* 指定された行数までのテーブル。
  たとえば、上記のサンプルクエリは、を実行することと同じです `T | take 50` 。
* 入力レコードセット内のレコードの数を保持する、1つの行または列を含むテーブル。
  たとえば、上記のサンプルクエリは、を実行することと同じです `T | count` 。

> [!TIP]
> に `evaluate` 、複雑なフィルターを含む表形式のソース、またはほとんどのソーステーブルの列を参照するフィルターがある場合は、関数を使用することをお勧めし [`materialize`](materializefunction.md) ます。 次に例を示します。

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```