---
title: .show データベース スキーマ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .show データベース スキーマについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 212aaf428e03190226a17509c97e9acd1394d2b1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519807"
---
# <a name="show-databases-schema"></a>.show データベース スキーマ

選択したデータベースの構造のフラット リストを返し、そのすべてのテーブルと列を 1 つのテーブルまたは JSON オブジェクトに格納します。
バージョンと共に使用した場合、データベースは、提供されたバージョンよりも新しいバージョンの場合にのみ返されます。

> [!NOTE]
> バージョンは"vMM.mm"形式でのみ提供する必要があります。 MMはメジャーバージョンを表し、mmはマイナーバージョンを表します。

`.show``database`*データベース名*`schema``details`[`if_later_than` ] [ *"バージョン"*] 

`.show``database`*データベース名*`schema``if_later_than` [ *"バージョン"*] `as``json`
 
`.show``databases``,`データベース名1 .. *DatabaseName1* `(``)` `schema` [`details` | `as` `json`]
 
`.show``databases``,`*"Version"**DatabaseName1*データベース名1if_later_than「バージョン」.. `(``)` `schema` [`details` | `as` `json`]

**例** 
 
データベース 'TestDB' には 'イベント' と呼ばれる 1 つのテーブルがあります。

```
.show database TestDB schema 
```

**出力例**

|DatabaseName|TableName|ColumnName|[列の型]|既定のテーブル|既定の列|プリティネーム|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|イベント|||True|False||       
|TestDB|イベント| 名前|System.String|True|False||     
|TestDB|イベント| StartTime|  System.DateTime|True|False||    
|TestDB|イベント| EndTime|    System.DateTime|True|False||        
|TestDB|イベント| City|   System.String|True| False||     
|TestDB|イベント| SessionId|  System.Int32|True|  True|| 

**例** 
 
```
.show database TestDB schema if_later_than "v1.0" 
```
**出力例**

|DatabaseName|TableName|ColumnName|[列の型]|既定のテーブル|既定の列|プリティネーム|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|イベント|||True|False||       
|TestDB|イベント| 名前|System.String|True|False||     
|TestDB|イベント| StartTime|  System.DateTime|True|False||    
|TestDB|イベント| EndTime|    System.DateTime|True|False||        
|TestDB|イベント| City|   System.String|True| False||     
|TestDB|イベント| SessionId|  System.Int32|True|  True||  

現在のデータベース バージョンより低いバージョンが提供されたため、'TestDB' スキーマが返されました。 同等以上のバージョンを指定すると、空の結果が生成されます。

**例** 
 
```
.show database TestDB schema as json
```

**出力例**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

