---
title: .alter 関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .alter 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 3fb7b3aab9d140d5f3660ef1384bf54efa9cd825
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522459"
---
# <a name="alter-function"></a>変更機能

既存の関数を変更し、データベース メタデータ内に格納します。
パラメータタイプと CSL ステートメントの規則は、[`let`ステートメント](../query/letstatement.md)の場合と同じです。

**構文**

```
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|出力パラメーター |Type |説明
|---|---|--- 
|名前  |String |関数の名前です。
|パラメーター  |String |関数に必要なパラメータ。
|Body  |String |(ゼロ以上)`let`ステートメントの後に、関数呼び出し時に評価される有効な CSL 式が続きます。
|Folder|String|UI 関数の分類に使用されるフォルダー。 このパラメーターは、関数の呼び出し方法を変更しません。
|ドクスト文字列|String|UI 用の関数の説明。

> [!NOTE]
> * 関数が存在しない場合は、エラーが返されます。 新しい関数を作成する場合は[、.create 関数](create-function.md)を参照してください。
> * [データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要
> * 関数を最初に作成した[データベース ユーザー](../management/access-control/role-based-authorization.md)は、関数を変更できます。 
> * すべての Kusto 型がステートメント`let`でサポートされているわけではありません。 サポートされるタイプは、文字列、長い、日時、時間範囲、倍精度浮動小数点数です。
> * 関数`skipvalidation`のセマンティック検証をスキップするために使用します。 これは、関数が誤った順序で作成され、F2 を使用する F1 が前に作成された場合に便利です。
 
**例** 

```
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|名前 |パラメーター |Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション2 |(マイリミット:ロング)| {ストームイベント&#124;私の制限を制限します}|マイフォルダ|パラメーター付きのデモ関数|