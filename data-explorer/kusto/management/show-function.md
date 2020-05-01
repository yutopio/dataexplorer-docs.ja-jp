---
title: . 関数を表示する-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの関数の表示について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa81df5ca89c1a7dc523d7799050b8a7ad3adaba
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617330"
---
# <a name="show-functions"></a>. 関数を表示する

現在選択されているデータベースに格納されているすべての関数を一覧表示します。
特定の関数を1つだけ返すには、「[関数を表示](#show-function)する」を参照してください。

```kusto
.show functions
```

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。
 
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメーター。
|Body  |String |(0 個以上)`let`ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。
 
**出力の例** 

|名前 |パラメーター|Body|Folder|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; 制限 100}|MyFolder|単純なデモ関数|
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|MyFolder|パラメーターを使用したデモ関数|
|MyFunction3 |() | {StormEvents 孔 (100)}|MyFolder|関数呼び出し (他の関数を)||

## <a name="show-function"></a>.show function

```kusto
.show function MyFunc1
```

1つの特定の格納関数の詳細を一覧表示します。 **すべて**の関数の一覧については、「[関数を表示する](#show-functions)」を参照してください。

**構文**

`.show``function` *FunctionName*

**出力**

|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。 
|パラメーター  |String |関数に必要なパラメーター。
|Body  |String |(0 個以上)`let`ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。
 
> [!NOTE] 
> * 関数が存在しない場合は、エラーが返されます。
> * [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。
 
**例** 

```kusto
.show function MyFunction1 
```
    
|名前 |パラメーター |Body|Folder|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; 制限 100}|MyFolder|単純なデモ関数
