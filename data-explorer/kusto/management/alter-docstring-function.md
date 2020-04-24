---
title: .alter 関数のドキュメント文字列 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .alter 関数のドキュメント文字列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25f6dac67f65add545f7215b44daafb0257a8375
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522578"
---
# <a name="alter-function-docstring"></a>変更関数のドキュメント文字列

既存の関数の DocString 値を変更します。

`.alter``function`*関数名の*`docstring`*ドキュメント*

> [!NOTE]
> * [データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要
> * 関数を最初に作成した[データベース ユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。 
> * 関数が存在しない場合は、エラーが返されます。 新しい関数を作成する場合は[、.create 関数](create-function.md)を参照してください。

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメータ。
|Body  |String |(ゼロ以上)`let`ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメーターは、関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。

**例** 

```
.alter function MyFunction1 docstring "Updated docstring"
```
    
|名前 |パラメーター |Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション2 |(マイリミット:ロング)| {ストームイベント&#124;私の制限を制限します}|マイフォルダ|更新されたドキュメント文字列|