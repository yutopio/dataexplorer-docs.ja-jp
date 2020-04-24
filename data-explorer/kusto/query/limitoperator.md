---
title: リミットオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの制限演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32b31fbef64d9ea689f427ea3b8ebd6fb0287090
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513296"
---
# <a name="limit-operator"></a>limit 演算子

指定した行数までを返します。

```kusto
T | limit 5
```

**エイリアス**

[take 演算子](takeoperator.md)