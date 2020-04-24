---
title: isnotempty() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの isempty() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14111be0fc0247dd151ef7454121e6b90a32ff0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513534"
---
# <a name="isnotempty"></a>isnotempty()

引数`true`が空の文字列でも null でもない場合に返します。

```kusto
isnotempty("") == false
```

**構文**

`isnotempty(`[*値*]`)`

`notempty(`[*値*]`)` -- エイリアス`isnotempty`