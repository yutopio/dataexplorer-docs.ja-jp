---
title: プレビュー プラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのプレビュー プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18bda0a4348d0c0eb2776bf124c57397f318a989
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510984"
---
# <a name="preview-plugin"></a>プレビュープラグイン

入力レコード セットから指定した行数まで、および入力レコード セット内のレコードの合計数を含むテーブルを返します。

```kusto
T | evaluate preview(50)
```

**構文**

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

**戻り値**

プラグイン`preview`は、2 つの結果テーブルを返します。
* 指定した行数までを含むテーブル。
  たとえば、上記のサンプル クエリは、 を`T | take 50`実行するのと同じです。
* 入力レコード セット内のレコード数を保持する、単一の行/列を持つテーブル。
  たとえば、上記のサンプル クエリは、 を`T | count`実行するのと同じです。

**ヒント**

複雑`evaluate`なフィルターを含む表形式のソース、またはソース テーブルの列のほとんどを参照するフィルターが前にある場合は、[`materialize`](materializefunction.md)この関数を使用することを好みます。 次に例を示します。

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```