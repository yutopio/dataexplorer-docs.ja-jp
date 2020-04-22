---
title: .drop 関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .drop 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8930f7333ff18fad0d5b3dbbebe9328f8bf7a0b9
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744775"
---
# <a name="drop-function"></a>.drop function

データベースから関数を削除します。
データベースから複数の関数を削除する場合は[、.drop 関数](#drop-functions)を参照してください。

**構文**

`.drop``function`*関数名*`ifexists`[ ]

* `ifexists`: 指定した場合、存在しない関数に対して失敗しないようにコマンドの動作を変更します。

> [!NOTE]
> * [データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要です。
> * 関数を最初に作成した[データベース ユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。  
    
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |削除された関数の名前
 
**例** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>.drop 関数

データベースから複数の関数を削除します。

**構文**

`.drop``functions` (*関数名 1*,*関数名 2*,..)[存在する]

**戻り値**

このコマンドは、データベース内の残りの関数のリストを返します。

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメータ。
|Body  |String |(ゼロ以上)`let`ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメーターは、関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。

[関数管理者の権限](../management/access-control/role-based-authorization.md)が必要です。

**例** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
