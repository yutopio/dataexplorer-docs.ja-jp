---
title: .show 関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .show 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7acfdb96570b067b0461f5a788b55fc8274c03f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519841"
---
# <a name="show-functions"></a>.show 関数

現在選択されているデータベースに格納されているすべての関数の一覧が表示されます。
特定の関数を 1 つだけ返す方法については[、「.show 関数](#show-function)」を参照してください。

```
.show functions
```

[データベース ユーザーの権限](../management/access-control/role-based-authorization.md)が必要です。
 
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメータ。
|Body  |String |(ゼロ以上)`let`ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメーターは、関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。
 
**出力例** 

|名前 |パラメーター|Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション1 |() | {ストームイベント&#124;制限 100}|マイフォルダ|簡単なデモ機能|
|マイファンクション2 |(マイリミット:ロング)| {ストームイベント&#124;私の制限を制限します}|マイフォルダ|パラメーター付きのデモ関数|
|マイファンクション3 |() | { ストームイベント(100) }|マイフォルダ|他の関数を呼び出す関数||

## <a name="show-function"></a>表示機能

```
.show function MyFunc1
```

特定のストアドファンクションの詳細を一覧表示します。 **すべての**関数の一覧については[、「.show 関数](#show-functions)」を参照してください。

**構文**

`.show``function`*関数名*

**出力**

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメータ。
|Body  |String |(ゼロ以上)`let`ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメータは関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。
 
> [!NOTE] 
> * 関数が存在しない場合は、エラーが返されます。
> * [データベース ユーザーの権限](../management/access-control/role-based-authorization.md)が必要です。
 
**例** 

```
.show function MyFunction1 
```
    
|名前 |パラメーター |Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション1 |() | {ストームイベント&#124;制限 100}|マイフォルダ|簡単なデモ機能