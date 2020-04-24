---
title: .alter 関数フォルダー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの .alter 関数フォルダーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 140991c723ebdd12fa17000ea845adbbfdd27771
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522544"
---
# <a name="alter-function-folder"></a>.alter 関数フォルダ

既存の関数の Folder 値を変更します。

`.alter``function`*関数名*`folder`*フォルダ*

> [!NOTE]
> * [データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要
> * 関数を最初に作成した[データベース ユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。 
> * 関数が存在しない場合は、エラーが返されます。 新しい関数を作成するには[、.create 関数](create-function.md)

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメーター。
|Body  |String |(ゼロ以上)Let ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメーターは、関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。

**例** 

```
.alter function MyFunction1 folder "Updated Folder"
```
    
|名前 |パラメーター |Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション2 |(マイリミット:ロング)| {ストームイベント&#124;私の制限を制限します}|更新されたフォルダ|いくつかのドクスト文字列|

```
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|名前 |パラメーター |Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション2 |(マイリミット:ロング)| {ストームイベント&#124;私の制限を制限します}|第1レベル\第2レベル|いくつかのドクスト文字列|