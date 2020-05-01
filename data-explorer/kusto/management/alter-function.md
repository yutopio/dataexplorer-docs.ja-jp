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
ms.openlocfilehash: d8248fdd9428df11a8e77316eec621102ddff9d6
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617801"
---
# <a name="alter-function"></a>.alter function

既存の関数を変更し、データベースメタデータ内に格納します。
パラメーターの型および CSL ステートメントの規則は、 [ `let`ステートメント](../query/letstatement.md)の場合と同じです。

**構文**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。
|パラメーター  |String |関数に必要なパラメーター。
|Body  |String |(0 個以上)`let`ステートメントの後に、関数の呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数のカテゴリ化に使用されるフォルダーです。 このパラメーターは、関数の呼び出し方法を変更しません。
|DocString|String|UI 用の関数の説明。

> [!NOTE]
> * 関数が存在しない場合は、エラーが返されます。 新しい関数を作成するには、「」を参照してください[。関数の作成](create-function.md)
> * [データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です
> * 関数を最初に作成した[データベースユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。 
> * ステートメントでは、すべての Kusto `let`型がサポートされているわけではありません。 サポートされている型は、string、long、datetime、timespan、および double です。
> * 関数`skipvalidation`のセマンティック検証をスキップするには、を使用します。 これは、関数が正しくない順序で作成され、F2 を使用する F1 が前に作成されている場合に便利です。
 
**例** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|名前 |パラメーター |Body|Folder|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|MyFolder|パラメーターを使用したデモ関数|
