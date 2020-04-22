---
title: .create-or-alter 関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .create 関数または alter 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 9e9c24f7fda44d6c44b8f78d8622b525268a341a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744273"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

ストアドファンクションを作成するか、既存の関数を変更してデータベースメタデータに格納します。

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

指定された*FunctionName を*持つ関数がデータベース メタデータに存在しない場合、コマンドは新しい関数を作成します。 関数が既に存在する場合は、その関数が変更されます。

**例**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|名前|パラメーター|Body|Folder|ドクスト文字列|
|---|---|---|---|---|
|テスト関数|(マイリミット:int)|{ ストームイベント&#124; myLimit }|マイフォルダ|パラメーター付きのデモ関数|
