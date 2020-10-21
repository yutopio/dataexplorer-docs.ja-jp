---
title: 論理 (二項) 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの論理 (二項) 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 6d1fcf7b9786951fedbdbf45d9e2c20a765452dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248512"
---
# <a name="logical-binary-operators"></a>論理 (二項) 演算子

次の論理演算子は、型の2つの値の間でサポートされてい `bool` ます。

> [!NOTE]
> これらの論理演算子は、ブール演算子と呼ばれることもありますが、場合によっては二項演算子とも呼ばれます。 名前はすべてのシノニムです。

|オペレーター名|構文|説明|
|-------------|------|-------|
|等式     |`==`  |`true`両方のオペランドが null 以外で互いに等しい場合に、を生成します。 それ以外の場合は `false`。|
|不等式   |`!=`  |`true`オペランドの一方 (または両方) が null であるか、または互いに等しくない場合に、を生成します。 それ以外の場合は `true`。|
|論理 AND  |`and` |`true`両方のオペランドがの場合にを生成 `true` します。|
|論理 OR   |`or`  |`true`オペランドの1つが `true` 、もう一方のオペランドに関係なく、の場合はを生成します。|

> [!NOTE]
> ブール値の null 値の動作により `bool(null)` 、2つのブール値の null 値は等しくも等しくもありません (つまり、 `bool(null) == bool(null)` との両方が値を `bool(null) != bool(null)` 生成し `false` ます)。
>
> 一方、 `and` / `or` null 値をと等価として扱い `false` `bool(null) or true` ます。したがって `true` 、はで `bool(null) and true` あり、はです `false` 。