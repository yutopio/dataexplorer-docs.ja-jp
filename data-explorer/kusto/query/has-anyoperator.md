---
title: has_anyオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーhas_anyオペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 42a181f7c75ef36119f7f2915fd5327d4a656eb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514316"
---
# <a name="has_any-operator"></a>has_any 演算子

`has_any`指定された値のセットに基づいて、演算子フィルターを使用します。

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**構文**

*T*`|``where`*スカラー式の T* *col*`has_any``(`リスト`)`   
*T* `|` T `where` *col* `has_any` col `(`*表形式の式*`)`   
 
**引数**

* *T* - レコードをフィルター処理する表形式の入力。
* *col* - フィルターする列。
* *式のリスト*- 表形式、スカラー式、またはリテラル式のコンマ区切りリスト  
* *表形式の式*- 値のセットを持つ表形式の式 (式に複数の列がある場合は、最初の列が使用されます)

**戻り値**

述部が対象となる*T*の行`true`

**メモ**

* 式リストは最大の値を`10,000`生成できます。    
* 表形式の式の場合、結果セットの最初の列が選択されます。   

**例:**  

**`has_any`演算子の簡単な使用法:**  

```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|State|count_|
|---|---|
|ニューヨーク|1750|
|ノースカロライナ|1721|
|サウスダコタ|1567|
|ニュージャージー|1044|
|サウスカロライナ|915|
|ノースダコタ|905|
|ニューメキシコ州|527|
|ニューハンプシャー|394|


**動的配列の使用:**

```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|State|count_|
|---|---|
|ノースカロライナ|1721|
|サウスダコタ|1567|
|サウスカロライナ|915|
|ノースダコタ|905|
|アトランティック サウス|193|
|アトランティック ノース|188|