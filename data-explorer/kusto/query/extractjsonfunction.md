---
title: extractjson ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの extractjson () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9668b173c6b3769113972be2c74382464e7d9819
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610578"
---
# <a name="extractjson"></a>extractjson()

パス式を使用している JSON テキストから、指定された要素を取得します。 

必要に応じて、抽出した文字列を特定の型に変換することもできます。

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

## <a name="syntax"></a>構文

`extractjson(`*Jsonpath* `,`*データソース*`)` 

## <a name="arguments"></a>引数

* *jsonpath*: JSON ドキュメントへのアクセサーを定義する jsonpath 文字列。
* *dataSource*: JSON ドキュメント。

## <a name="returns"></a>戻り値

この関数は、有効な JSON 文字列が含まれている dataSource への JsonPath クエリを実行します。オプションで、その値を 3 番目の引数に基づいて他の型に変換することもできます。

## <a name="example"></a>例

`[`角かっこの `]` 表記とドット ( `.` ) は等価です。

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON パス式

|パス式|説明|
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