---
title: ブール データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのブール データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: dd9cfa4f97f3baa4353f967f5e1ca31186f4815f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509930"
---
# <a name="the-bool-data-type"></a>ブールデータ型

( `bool` `boolean`) データ型は、2 つの状態`true`のいずれか`false`、または (それぞれと`1`で`0`内部的にエンコードされ、 と ) と null 値を持つことができます。

## <a name="bool-literals"></a>ブールリテラル

データ`bool`型には、次のリテラルがあります。
* `true`そして`bool(true)`: 真の表現
* `false`そして`bool(false)`: 虚偽を表す
* `bool(null)`: [NULL 値を](null-values.md)参照してください。

## <a name="bool-operators"></a>ブール演算子

データ`bool`型は、等値 (`==`) 、等値`!=`( ) 、`and`論理と ( )`or`、および論理演算子 ( ) 演算子をサポートします。