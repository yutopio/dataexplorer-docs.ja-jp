---
title: . 関数の作成-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーで関数を作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0a509e33ca1bc4c6d4a400428898b7c241222a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320980"
---
# <a name="create-function"></a>.create function

指定した名前の再利用可能な[ `let` ステートメント](../query/letstatement.md)関数である、格納されている関数を作成します。 関数の定義は、データベースのメタデータと共に保持されます。

関数は他の関数を呼び出すことができます (recursiveness はサポートされていません)。また、 `let` ステートメントは *関数本体* の一部として許可されます。 [ `let` ステートメント](../query/letstatement.md)を参照してください。

パラメーターの型および CSL ステートメントの規則は、 [ `let` ステートメント](../query/letstatement.md)の場合と同じです。
    
**構文**

`.create``function`[ `ifnotexists` ] [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ] [ `,` `skipvalidation` `=` ' true '] `)` ] *FunctionName* `(` *ParamName* `:` *paramtype* [ `,` ...] `)` `{` *functionbody*`}`

**出力**    
    
|出力パラメーター |種類 |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメーター。
|本文  |String |(0 個以上) `let` ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。

> [!NOTE]
> * 関数が既に存在する場合:
>    * `ifnotexists`フラグが指定されている場合、コマンドは無視されます (変更は適用されません)。
>    * `ifnotexists`フラグが指定されていない場合は、エラーが返されます。
>    * 既存の関数を変更する場合は、「」を参照してください。 [`.alter function`](alter-function.md)
> * [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。
> * ステートメントでは、すべてのデータ型がサポートされているわけではありません `let` 。 サポートされる型は、boolean、string、long、datetime、timespan、double、および dynamic です。
> * 関数のセマンティック検証をスキップするには、' skipvalidation ' を使用します。 これは、関数が正しくない順序で作成され、F2 を使用する F1 が前に作成されている場合に便利です。

**例** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|名前|パラメーター|本文|Folder|DocString|
|---|---|---|---|---|
|MyFunction1|()|{StormEvents &#124; 制限 100}|デモ|単純なデモ関数|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|名前|パラメーター|本文|Folder|DocString|
|---|---|---|---|---|
|MyFunction2|(myLimit: long)|{StormEvents &#124; 制限 myLimit}|デモ|パラメーターを使用したデモ関数|
