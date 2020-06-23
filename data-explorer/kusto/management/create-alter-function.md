---
title: . create-または alter function-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの create/alter 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f19ca38f344f10b9dd8e4491b467eaad5ca022bc
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197244"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

格納されている関数を作成するか、既存の関数を変更してデータベースメタデータ内に格納します。

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

指定された*FunctionName*の関数がデータベースメタデータに存在しない場合、コマンドは新しい関数を作成します。 それ以外の場合は、その関数が変更されます。

**例**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|名前|パラメーター|本文|フォルダー|DocString|
|---|---|---|---|---|
|TestFunction|(myLimit: int)|{StormEvents &#124; myLimit を受け取る}|MyFolder|パラメーターを使用したデモ関数|
