---
title: パック() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの pack() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7b43be96f272ab929434f10cac910bd4072e650
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511834"
---
# <a name="pack"></a>pack()

名前と`dynamic`値のリストからオブジェクト (プロパティ バッグ) を作成します。

エイリアスを`pack_dictionary()`使用して機能します。

**構文**

`pack(`*キー1*`,`*値1*`,`*キー2*`,`*値2*`,... )`

**引数**

* キーと値の交互のリスト (リストの全長は偶数である必要があります)
* すべてのキーは空でない定数文字列でなければなりません

**使用例**

次の例は、 `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`を返します。

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

2つのテーブル、SmsMessagesとMmsMessagesを取ることができます:

テーブル Sms メッセージ 

|ソース番号 |ターゲット番号| 文字数
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

テーブル Mms メッセージ 

|ソース番号 |ターゲット番号| アタッシェムネットサイズ | アタッシェネットタイプ | アタッシェネット名
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | JPEG | ピック1
|555-555-1234 |555-555-1212 | 250 | JPEG | ピック2
|555-555-1234 |555-555-1213 | 300 | png | ピック3

次のクエリ:
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmnetSize", AttachmnetSize, "AttachmnetType", AttachmnetType, "AttachmnetName", AttachmnetName))
| where SourceNumber == "555-555-1234"
``` 

戻り値:

|TableName |ソース番号 |ターゲット番号 | パック
|---|---|---|---
|メッセージ|555-555-1234 |555-555-1212 | {"文字数": 46}
|メッセージ|555-555-1234 |555-555-1213 | {"文字数": 50}
|メッセージ|555-555-1234 |555-555-1212 | {"添付ネットサイズ": 250, "添付ネットタイプ": "jpeg", "アタッシェネット名": "Pic2"}
|メッセージ|555-555-1234 |555-555-1213 | {"添付ネットサイズ": 300, "添付ネットタイプ": "png", "アクトネット名": "Pic3"}