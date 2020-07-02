---
title: . alter function docstring-Azure データエクスプローラー
description: この記事 `.alter function docstring` では、Azure データエクスプローラーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cad374c767b126d60b7c701f596bddf3c20c4345
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763882"
---
# <a name="alter-function-docstring"></a>. alter function docstring

`DocString`既存の関数の値を変更します。

`.alter``function` *FunctionName*の `docstring` *ドキュメント*

> [!NOTE]
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * 関数を最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。
> * 関数が存在しない場合は、エラーが返されます。 新しい関数を作成する方法の詳細については、「」を参照してください[。関数の作成](create-function.md)

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前
|パラメーター  |String |関数に必要なパラメーター
|本文  |String |(0 個以上) `let`ステートメントの後に、関数が呼び出されたときに評価される有効な CSL 式が続きます。
|フォルダー|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明

**例** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|名前 |パラメーター |本文|フォルダー|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|MyFolder|更新された docstring|
