---
title: . alter 関数-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの alter 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: fc7c29542f63ea9b659bd3318d1442d9bca4ae48
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321711"
---
# <a name="alter-function"></a>.alter function

既存の関数を変更し、データベースメタデータ内に格納します。
パラメーターの型および CSL ステートメントの規則は、 [ `let` ステートメント](../query/letstatement.md)の場合と同じです。

**構文**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|出力パラメーター |種類 |説明
|---|---|--- 
|名前  |String |関数の名前です。
|パラメーター  |String |関数に必要なパラメーター。
|本文  |String |(0 個以上) `let` ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。

> [!NOTE]
> * 関数が存在しない場合は、エラーが返されます。 新しい関数の作成については、「」を参照してください。 [`.create function`](create-function.md)
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * 関数を最初に作成した [データベースユーザー](../management/access-control/role-based-authorization.md) は、関数を変更できます。 
> * ステートメントでは、すべての Kusto 型がサポートされているわけではありません `let` 。 サポートされている型は、string、long、datetime、timespan、および double です。
> * `skipvalidation`関数のセマンティック検証をスキップするには、を使用します。 これは、関数が正しくない順序で作成され、F2 を使用する F1 が前に作成されている場合に便利です。
 
**例** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|名前 |パラメーター |本文|Folder|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|MyFolder|パラメーターを使用したデモ関数|
