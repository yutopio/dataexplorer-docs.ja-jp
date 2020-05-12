---
title: has_any 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの has_any 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 19329b8822a1e1d484c5f751f5fbc2f8eb6343ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226739"
---
# <a name="has_any-operator"></a>has_any 演算子

`has_any`演算子は、指定された値のセットに基づいてフィルター処理を行います。

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**構文**

*T* `|` `where` *col* `has_any` `(` *スカラー式の*T col リスト`)`   
*T* `|` `where` *列* `has_any` `(` *表形式式*`)`   
 
**引数**

* レコードをフィルター処理する*T*テーブルの入力。
* *列-フィルター*処理します。
* *式の一覧*-表形式、スカラー式、またはリテラル式のコンマ区切りの一覧  
* *表形式の式*-値のセットを含む表形式の式-式に複数の列がある場合は、最初の列が使用されます。

**戻り値**

述語がある*T*内の行`true`

**メモ**

* 式リストでは、最大値を生成でき `10,000` ます。    
* テーブル式の場合は、結果セットの最初の列が選択されます。   

**例:**  

**演算子の単純な使用法は `has_any` 次のとおりです。**  

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|新しいメキシコ|527|
|新しいニューハンプシャー|394|


**動的配列の使用:**

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|大西洋南部|193|
|大西洋北部|188|
