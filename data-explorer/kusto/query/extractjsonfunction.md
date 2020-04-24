---
title: 抽出json() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの extractjson() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6177a1c8a6ed4390093e6f6fd24c5f5e9fd04f8a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515336"
---
# <a name="extractjson"></a>extractjson()

パス式を使用している JSON テキストから、指定された要素を取得します。 

必要に応じて、抽出した文字列を特定の型に変換することもできます。

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

**構文**

`extractjson(`*データ*`,`*ソース*`)` 

**引数**

* *jsonPath:* JSON ドキュメントへのアクセサーを定義する JsonPath 文字列。
* *データソース*: JSON ドキュメント。

**戻り値**

この関数は、有効な JSON 文字列が含まれている dataSource への JsonPath クエリを実行します。オプションで、その値を 3 番目の引数に基づいて他の型に変換することもできます。

**例**

`[`括弧`]`表記とドット ()`.`表記は同等です。

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON パス式

|||
|---|---|
|`$`|ルート オブジェクト|
|`@`|現在のオブジェクト|
|`.` または `[ ]` | 子|
|`[ ]`|配列インデックス|

*(現在、ワイルドカード、再帰、共用体、およびスライスは実装していません。)*


**パフォーマンスに関するヒント**

* `extractjson()`
* 代わりに、 [extract](extractfunction.md) による正規表現の一致を使用することを検討してください。 こちらの方が実行速度が非常に速く、JSON がテンプレートから生成される場合に効率的です。
* JSON から複数の値を抽出する必要がある場合は、 `parse_json()` を使用してください。
* 列の型が動的になるように宣言することによって、取り込み時に JSON が解析されるようにすることを検討してください。