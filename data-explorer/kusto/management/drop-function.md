---
title: . drop 関数-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの drop 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 279af228d15f511b35c26eebd0d21521450be786
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954741"
---
# <a name="drop-function"></a>.drop function

データベースから関数を削除します。
データベースから複数の関数を削除する方法については、「」を参照してください[。](#drop-functions)

**構文**

`.drop``function` *FunctionName* [ `ifexists` ]

* `ifexists`: 指定した場合、存在しない関数に対して失敗しないように、コマンドの動作を変更します。

> [!NOTE]
> * [Function admin 権限](../management/access-control/role-based-authorization.md)が必要です。
    
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |削除された関数の名前
 
**例** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>. drop 関数

複数の関数をデータベースから削除します。

**構文**

`.drop``functions`(*FunctionName1*、 *FunctionName2*,..) [ifexists]

**戻り値**

このコマンドは、データベース内の残りの関数の一覧を返します。

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメーター。
|Body  |String |(0 個以上) `let` ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。

[Function admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**例** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
