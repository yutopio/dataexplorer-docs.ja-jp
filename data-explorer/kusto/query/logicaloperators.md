---
title: 論理 (バイナリ) 演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの論理 (バイナリ) 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 53505067b93234aa89849e1c66fe2f77a58e0f26
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513092"
---
# <a name="logical-binary-operators"></a>論理 (二項) 演算子

次の論理演算子は、型の 2`bool`つの値の間でサポートされています。

> [!NOTE]
> これらの論理演算子は、ブール演算子と呼ばれる場合があり、バイナリ演算子と呼ばれることもあります。 名前はすべて同義語です。

|オペレーター名|構文|意味|
|-------------|------|-------|
|等価比較     |`==`  |両方`true`のオペランドが NULL 以外で、互いに等しい場合に生成されます。 それ以外の場合は `false`。|
|非等値   |`!=`  |オペランド`true`の一方 (またはその両方) が NULL であるか、または互いに等しくない場合に生成されます。 それ以外の場合は `true`。|
|論理的および  |`and` |両方`true`のオペランドが の`true`場合は、結果が得られます。|
|論理または   |`or`  |一`true`方のオペランドが`true`が 、もう一方のオペランドに関係なく が場合に生成されます。|

> [!NOTE]
> ブール値の`bool(null)`null 値の動作により、2 つのブール値は等しくも等しくない (つまり、`bool(null) == bool(null)`両方`bool(null) != bool(null)`とも値`false`を返す) ではありません。
>
> 一方、null`and`/`or``false`値は と`bool(null) or true``true``bool(null) and true`同等として扱います。 `false`