---
title: isnotempty ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの isnotempty () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f7811c2668bd0bb2d6e3711800ee5d9a450b9ec
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056236"
---
# <a name="isnotempty"></a>isnotempty()

引数が空の文字列ではなく、null でない場合は、を返し `true` ます。

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Syntax

`isnotempty(`[*値*]`)`

`notempty(`[*値*] `)` --のエイリアス `isnotempty`

## <a name="example"></a>例

```kusto
T
| where isnotempty(fieldName)
| count
```
