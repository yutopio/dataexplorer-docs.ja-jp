---
title: isnotnull ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isnotnull () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 44161dcdc34232225f4b133fe0a569fb818c3f0f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349377"
---
# <a name="isnotnull"></a>isnotnull()

`true`引数が null でない場合は、を返します。

## <a name="syntax"></a>構文

`isnotnull(`[ *値* ]`)`

`notnull(`[ *値* ] `)` -のエイリアス `isnotnull`

## <a name="example"></a>例

```kusto
T | where isnotnull(PossiblyNull) | count
```
