---
title: .create 関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .create 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc2caa5dcc886964b42bf1915bf3a003f2d32c7a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744464"
---
# <a name="create-function"></a>.create function

指定された名前を持つ再利用可能な[`let`ステートメント](../query/letstatement.md)関数であるストアド関数を作成します。 関数定義は、データベース メタデータで永続化されます。

関数は他の関数を呼び出すことができます (再`let`帰性はサポートされていません) し、ステートメントは*Function Body*の一部として許可されます。 [`let`ステートメント](../query/letstatement.md)を参照してください。

パラメータタイプと CSL ステートメントの規則は、[`let`ステートメント](../query/letstatement.md)の場合と同じです。
    
**構文**

`.create``function` `=` `)``,` `skipvalidation` `,` `:` `)``,` `folder` `=` *Documentation*`with` `(``docstring` `=` *FunctionName* `(` *ParamName* *FolderName* *ParamType* [ ] [ [ ドキュメント ] [ フォルダ名 ] ] [ [ true'] ] 関数名 パラム名 パラムタイプ [ ..`ifnotexists``)``{`*ファンクションボディ*`}`

**出力**    
    
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメータ。
|Body  |String |(ゼロ以上)`let`ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメーターは、関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。

> [!NOTE]
> * 関数が既に存在する場合:
>    * flag`ifnotexists`を指定すると、コマンドは無視されます (変更は適用されません)。
>    * フラグ`ifnotexists`が指定されていない場合は、エラーが返されます。
>    * 既存の関数を変更する場合は[、.alter 関数](alter-function.md)を参照してください。
> * [データベース ユーザーの権限](../management/access-control/role-based-authorization.md)が必要です。
> * ステートメントでは`let`、すべてのデータ型がサポートされるわけではありません。 サポートされるタイプは、ブール型、文字列型、長い型、日時型、時間範囲、倍精度浮動小数点型、動的型です。
> * 関数のセマンティック検証をスキップするには、'skipvalidation' を使用します。 これは、関数が誤った順序で作成され、F2 を使用する F1 が前に作成された場合に便利です。

**使用例** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|名前|パラメーター|Body|Folder|ドクスト文字列|
|---|---|---|---|---|
|マイファンクション1|()|{ストームイベント&#124;制限 100}|デモ|簡単なデモ機能|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|名前|パラメーター|Body|Folder|ドクスト文字列|
|---|---|---|---|---|
|マイファンクション2|(マイリミット:ロング)|{ストームイベント&#124;私の制限を制限します}|デモ|パラメーター付きのデモ関数|
