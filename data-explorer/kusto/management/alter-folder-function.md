---
title: . alter 関数フォルダー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの alter function フォルダーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 6becb5e29fd5771e1027c824b5a3539ed3c33b88
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617835"
---
# <a name="alter-function-folder"></a>. alter function フォルダー

既存の関数のフォルダー値を変更します。

`.alter``function` *FunctionName* FunctionName `folder` *フォルダー*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * 関数を最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。 
> * 関数が存在しない場合は、エラーが返されます。 新しい関数を作成する場合は、 [create function](create-function.md)

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメーター。
|Body  |String |(0 個以上)ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式を指定します。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。

**例** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|名前 |パラメーター |Body|Folder|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|更新されたフォルダー|いくつかの DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|名前 |パラメーター |Body|Folder|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|第1レベルのレベル|いくつかの DocString|