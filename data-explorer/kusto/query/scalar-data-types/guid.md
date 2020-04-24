---
title: GUID データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの guid データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 382b2da0ab791cf3e2fea0e1c785ee42634aab07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509862"
---
# <a name="the-guid-data-type"></a>GUID データ型

( `guid` `uuid`, `uniqueid`) データ型は、128 ビットのグローバル一意値を表します。

> [!WARNING]
> このドキュメントの執筆時点では、`guid` 型のサポートは不完全です。
> 主なギャップは、この型の列にインデックスが存在しないほど、この型に対する述語を実行するクエリのパフォーマンスに影響を与えるということです。
> 代わりに、`string` 型の値を使用することを強くお勧めします。

## <a name="guid-literals"></a>guid リテラル

型`guid`のリテラルを表す場合は、次の形式を使用します。

```kusto
guid(74be27de-1e4e-49d9-b579-fe0b331d3642)
```

特殊形式`guid(null)`は[null 値](null-values.md)を表します。