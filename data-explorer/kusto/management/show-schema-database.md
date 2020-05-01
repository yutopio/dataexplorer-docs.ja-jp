---
title: 。データベーススキーマを表示します-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのデータベーススキーマの表示について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90a48ec3a830e754bf823712ca7016d162474a39
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616985"
---
# <a name="show-databases-schema"></a>。データベーススキーマを表示します。

1つのテーブルまたは JSON オブジェクト内のすべてのテーブルと列を含む、選択されたデータベースの構造の単純なリストを返します。
バージョンで使用する場合は、指定されたバージョンよりも新しいバージョンである場合にのみ、データベースが返されます。

> [!NOTE]
> バージョンは、"vMM.mm" 形式でのみ指定する必要があります。 MM はメジャーバージョンを表し、mm はマイナーバージョンを表します。

`.show``database` *DatabaseName* DatabaseName `schema` [`details`] [`if_later_than` *"Version"*] 

`.show``database` *DatabaseName* DatabaseName `schema` [`if_later_than` *"Version"*] `as``json`
 
`.show``databases` `,` *DatabaseName1* DatabaseName1 `(` ...`)` `schema` [`details` | `as` `json`]
 
`.show``databases` *"Version"* `,` *DatabaseName1* DatabaseName1 if_later_than "Version"... `(``)` `schema` [`details` | `as` `json`]

**例** 
 
データベース ' TestDB ' には、' Events ' という名前のテーブルが1つ含まれています。

```kusto
.show database TestDB schema 
```

**出力例**

|DatabaseName|TableName|ColumnName|[列の型]|IsDefaultTable|IsDefaultColumn|"この名前"|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1.1       
|TestDB|events|||True|False||       
|TestDB|events| 名前|System.String|True|False||     
|TestDB|events| StartTime|  System.DateTime|True|False||    
|TestDB|events| EndTime|    System.DateTime|True|False||        
|TestDB|events| City|   System.String|True| False||     
|TestDB|events| SessionId|  System.Int32|True|  True|| 

**例** 
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```
**出力例**

|DatabaseName|TableName|ColumnName|[列の型]|IsDefaultTable|IsDefaultColumn|"この名前"|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1.1       
|TestDB|events|||True|False||       
|TestDB|events| 名前|System.String|True|False||     
|TestDB|events| StartTime|  System.DateTime|True|False||    
|TestDB|events| EndTime|    System.DateTime|True|False||        
|TestDB|events| City|   System.String|True| False||     
|TestDB|events| SessionId|  System.Int32|True|  True||  

現在のデータベースバージョンよりも古いバージョンが指定されたため、' TestDB ' スキーマが返されました。 等しいまたはそれ以降のバージョンを指定すると、空の結果が生成されます。

**例** 
 
```kusto
.show database TestDB schema as json
```

**出力例**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

