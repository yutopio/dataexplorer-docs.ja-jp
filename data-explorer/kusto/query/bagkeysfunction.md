---
title: bag_keys() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの bag_keys() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a73798dff27bf9f4c7d554d97804aef6b49e1de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518209"
---
# <a name="bag_keys"></a>bag_keys()

動的プロパティ バッグ オブジェクト内のすべてのルート キーを列挙します。

`bag_keys(`*動的オブジェクト*`)`

**戻り値**

キーの配列は、順序が未定です。

**使用例**

|式|評価結果|
|---|---|
|`bag_keys(dynamic({'a':'b', 'c':123}))` | `['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':{'d':123}})) `|`['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':[{'d':123}]})) `|`['a','c']`|
|`bag_keys(dynamic(null))`|`null`|
|`bag_keys(dynamic({}))`|`[]`|
|`bag_keys(dynamic('a'))`|`null`|
|`bag_keys(dynamic([]))  `|`null`|