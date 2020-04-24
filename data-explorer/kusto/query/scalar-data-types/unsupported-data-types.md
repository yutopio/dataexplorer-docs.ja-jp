---
title: サポートされていないスカラー データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでサポートされていないスカラー データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 76a548abdf05cec57e4d0558e5d98ab0f7cbdc3e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509590"
---
# <a name="unsupported-scalar-data-types"></a>サポートされていないスカラー データ型

ドキュメント化されていないス**カラー データ型**はすべて、Kusto ではサポートされないと見なされます。

サポートされていない型の中には、次のものがあります。 以前はサポートされていたタイプがあります。

| Type       | その他の名前   | 同等の .NET 型              | ストレージの種類 (内部名)|
| ---------- | -------------------- | --------------------------------- | ----------------------------|
| `float`    |                      | `System.Single`                   | `R32`                       |
| `int16`    |                      | `System.Int16`                    | `I16`                       |
| `uint16`   |                      | `System.UInt16`                   | `UI16`                      |
| `uint32`   | `uint`               | `System.UInt32`                   | `UI32`                      |
| `uint64`   | `ulong`              | `System.UInt64`                   | `UI64`                      |
| `uint8`    | `byte`               | `System.Byte`                     | `UI8`                       |