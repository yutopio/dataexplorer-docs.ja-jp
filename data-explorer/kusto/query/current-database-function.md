---
title: current_database ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの current_database () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b4e0458c78023d35e002910de4c5946260b43b4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252547"
---
# <a name="current_database"></a>current_database()

スコープ内のデータベースの名前を返します (他のデータベースが指定されていない場合、すべてのクエリエンティティが解決されるデータベース)。

## <a name="syntax"></a>構文

`current_database()`

## <a name="returns"></a>戻り値

スコープ内のデータベースの名前を型の値として指定し `string` ます。

## <a name="example"></a>例

```kusto
print strcat("Database in scope: ", current_database())
```