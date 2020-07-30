---
title: pack_all ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの pack_all () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6c157c014ec3b83aa39d4bdfcadda12e97e84f3e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346543"
---
# <a name="pack_all"></a>pack_all()

`dynamic`表形式の式のすべての列からオブジェクト (プロパティバッグ) を作成します。

## <a name="syntax"></a>構文

`pack_all()`

**メモ**

返されたオブジェクトの表現は、実行間でバイトレベルの互換性が保証されていません。 たとえば、バッグに表示されるプロパティは、異なる順序で表示される場合があります。

## <a name="examples"></a>例

指定されたテーブル SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

次のクエリ:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(SourceNumber:string,TargetNumber:string,CharsCount:long)
[
'555-555-1234','555-555-1212',46,
'555-555-1234','555-555-1213',50,
'555-555-1212','555-555-1234',32
]
| extend Packed=pack_all()
```
戻り値:

|TableName |SourceNumber |TargetNumber | パック
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"SourceNumber": "555-555-1234", "TargetNumber": "555-555-1212", "CharsCount":46}
|SmsMessages|555-555-1234 |555-555-1213 | {"SourceNumber": "555-555-1234", "TargetNumber": "555-555-1213", "CharsCount":50}
|SmsMessages|555-555-1212 |555-555-1234 | {"SourceNumber": "555-555-1212", "TargetNumber": "555-555-1234", "CharsCount":32}
