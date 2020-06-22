---
title: スカラー データ型 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer のスカラー データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 95bb28c81ec3221569758ead8a289bdf81d32d3d
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128650"
---
# <a name="scalar-data-types"></a>スカラー データ型

すべてのデータ値 (式の値や関数のパラメーターなど) には、**データ型**があります。 データ型は、**スカラー データ型** (以下に示す組み込みで定義済みの型のいずれか)、または**ユーザー定義レコード** (テーブルの行のデータ型など、名前/スカラー データ型のペアの順序付けされたシーケンス) のいずれかです。

Kusto には、Kusto で使用できるすべての種類のデータを定義する一連のシステム データ型が用意されています。

> [!NOTE]
> ユーザー定義のデータ型は Kusto ではサポートされていません。

次の表に、Kusto でサポートされているデータ型と、それらを参照するために使用できる追加の別名、およびほぼ同等の .NET Framework の型を示します。

| Type       | その他の名前   | 同等の .NET 型              | gettype()   |
| ---------- | -------------------- | --------------------------------- | ----------- |
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |
| `dynamic`  |                      | `System.Object`                   | `array` または `dictionary`、あるいはその他の任意の値 |
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |
| `int`      |                      | `System.Int32`                    | `int`       |
| `long`     |                      | `System.Int64`                    | `long`      |
| `real`     | `double`             | `System.Double`                   | `real`      |
| `string`   |                      | `System.String`                   | `string`    |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   |

文字列以外のすべてのデータ型には、データの欠如またはデータの不一致を表す特殊な "null" 値が含まれます。 たとえば、文字列 `"abc"` を `int` 列に取り込もうとするとこの値になります。
この値を明示的に具体化することはできませんが、`isnull()` 関数を使用することにより、式がこの値に評価されるかどうかを検出できます。

> [!WARNING]
> `guid` 型のサポートは不完全です。
> 代わりに、`string` 型の値を使用することを強くお勧めします。
