---
title: プレビュープラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのプレビュープラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7c4ee69c4f82c25c6f4cf7d4b63ad9a659892a28
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802997"
---
# <a name="preview-plugin"></a>preview プラグイン

入力レコードセットから指定された行数までのテーブルと、入力レコードセット内のレコードの合計数を返します。

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>構文

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

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