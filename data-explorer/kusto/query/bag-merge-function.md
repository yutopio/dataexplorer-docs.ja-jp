---
title: bag_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bag_merge () について説明します。
services: data-explorer
author: elgevork
ms.author: elgevork
ms.reviewer: ''
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: f5ca4888b0c3aad976d78c10bbbfa0810483c75b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784601"
---
# <a name="bag_merge"></a>bag_merge()

入力動的プロパティバッグオブジェクトを動的プロパティバッグオブジェクトにマージします。

**構文**

`bag_merge(`*動的オブジェクト* `,`*動的オブジェクト*`)`

**引数**

* コンマで区切られた動的オブジェクトを入力します。 関数では、2 ~ 64 の入力動的オブジェクトがサポートされています。

**戻り値**

`dynamic`プロパティバッグを返します。 すべての入力プロパティバッグオブジェクトを結合した結果。
複数の入力オブジェクトにキーが表示される場合は、任意の値 (このキーで使用可能な値のうちのいずれか) が選択されます。

**例**

式:

`print result = bag_merge(dynamic({ 'A1':12, 'B1':2, 'C1':3}), dynamic({ 'A2':81, 'B2':82, 'A1':1}))`

評価結果:

`{
  "A1": 12,
  "B1": 2,
  "C1": 3,
  "A2": 81,
  "B2": 82
}`
