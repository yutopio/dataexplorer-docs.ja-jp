---
title: pack ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの pack () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94629ae8f4795e28cbfb0c41f06596731cdd8d9
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717327"
---
# <a name="pack"></a>pack()

`dynamic`名前と値のリストからオブジェクト (プロパティバッグ) を作成します。

エイリアスが `pack_dictionary()` 機能します。

**構文**

`pack(`*key1* `,`*value1* `,`*key2* `,`*value2*`,... )`

**引数**

* キーと値の交互のリスト (リスト全体の長さは偶数である必要があります)
* すべてのキーは、空でない定数文字列である必要があります

**使用例**

次の例は、 `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`を返します。

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

では、SmsMessages と MmsMessages という2つのテーブルを取得できます。

テーブル SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

テーブル MmsMessages 

|SourceNumber |TargetNumber| AttachmentSize | Attachmenttype が | 添付名
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | jpeg | Pic1
|555-555-1234 |555-555-1212 | 250 | jpeg | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

次のクエリ:
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmentSize", AttachmentSize, "AttachmentType", AttachmentType, "AttachmentName", AttachmentName))
| where SourceNumber == "555-555-1234"
``` 

戻り値:

|TableName |SourceNumber |TargetNumber | パック
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"CharsCount":46}
|SmsMessages|555-555-1234 |555-555-1213 | {"CharsCount":50}
|MmsMessages|555-555-1234 |555-555-1212 | {"AttachmentSize": 250、"Attachmentsize": "jpeg"、"Attachmentsize": "Pic2"}
|MmsMessages|555-555-1234 |555-555-1213 | {"AttachmentSize": 300、"Attachmentsize": "png"、"Attachmentsize": "Pic3"}
