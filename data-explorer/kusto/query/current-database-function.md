---
title: current_database ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの current_database () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d68c35547c840cc1e16224c376e90dfabec296d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348702"
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