---
title: ファセット演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのファセット演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0e5bc062b99a97b8d11c11312aac2d5829d6584b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348022"
---
# <a name="facet-operator"></a>facet 演算子

指定された列ごとに1つずつ、一連のテーブルを返します。
各テーブルは、列によって取得される値のリストを指定します。
句を使用して、追加のテーブルを作成でき `with` ます。

## <a name="syntax"></a>構文

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *filterpipe*`)`

## <a name="arguments"></a>引数

* *ColumnName:* 出力テーブルとして集約される、入力内の列の名前。
* *Filterpipe:* 出力の1つを生成するために入力テーブルに適用されるクエリ式。

## <a name="returns"></a>戻り値

複数のテーブル: 句に1つ、 `with` 列ごとに1つ。

## <a name="example"></a>例

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```