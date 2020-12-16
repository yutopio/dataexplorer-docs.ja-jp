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
ms.openlocfilehash: 4f3639aeb6e401aa37703bbef929af2275960a91
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513971"
---
# <a name="show-database-schema-commands"></a>。データベーススキーマコマンドを表示します。

次のコマンドは、データベーススキーマをテーブル、JSON オブジェクト、または CSL スクリプトとして表示します。

## <a name="show-databases-schema"></a>。データベーススキーマを表示します。

**構文**

`.show``database` *DatabaseName* `schema` [ `details` ] [ `if_later_than` *"Version"*] 

`.show``databases` `(` *DatabaseName1* `,` .. `)` . `schema``details` 
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ... `)` `schema``details`

**戻り値**

1つのテーブルまたは JSON オブジェクト内のすべてのテーブルと列を含む、選択されたデータベースの構造の単純なリストを返します。
バージョンで使用する場合は、指定されたバージョンよりも新しいバージョンである場合にのみ、データベースが返されます。

> [!NOTE]
> バージョンは、"vMM.mm" 形式でのみ指定する必要があります。 MM はメジャーバージョンを表し、mm はマイナーバージョンを表します。

**例** 
 
データベース ' TestDB ' には、' Events ' という名前のテーブルが1つ含まれています。

```kusto
.show database TestDB schema 
```

**出力**

|DatabaseName|TableName|ColumnName|[列の型]|IsDefaultTable|IsDefaultColumn|"この名前"|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1.1       
|TestDB|イベント|||正|誤||       
|TestDB|イベント| 名前|System.String|正|誤||     
|TestDB|イベント| StartTime|  System.DateTime|正|誤||    
|TestDB|イベント| EndTime|    System.DateTime|正|誤||        
|TestDB|イベント| City|   System.String|正| 誤||     
|TestDB|イベント| SessionId|  System.Int32|True|  True|| 

**例** 

次の例では、指定されたバージョンより新しいバージョンである場合にのみ、データベースが返されます。
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**出力**

|DatabaseName|TableName|ColumnName|[列の型]|IsDefaultTable|IsDefaultColumn|"この名前"|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v1.1       
|TestDB|イベント|||正|誤||       
|TestDB|イベント| 名前|System.String|正|誤||     
|TestDB|イベント| StartTime|  System.DateTime|正|誤||    
|TestDB|イベント| EndTime|    System.DateTime|正|誤||        
|TestDB|イベント| City|   System.String|正| 誤||     
|TestDB|イベント| SessionId|  System.Int32|True|  True||  

現在のデータベースバージョンよりも古いバージョンが指定されたため、' TestDB ' スキーマが返されました。 等しいまたはそれ以降のバージョンを指定すると、空の結果が生成されます。

## <a name="show-database-schema-as-json"></a>。データベーススキーマを json として表示します

**構文**

`.show``database` *DatabaseName* `schema` [ `if_later_than` *"Version"*] `as``json`
 
`.show``databases` `(` *DatabaseName1* `,` . `)` . `schema` . `as``json`
 
`.show``databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ... `)` `schema` `as``json`

**戻り値**

すべてのテーブルと列を JSON オブジェクトとして使用して、選択したデータベースの構造の単純なリストを返します。
バージョンで使用する場合は、指定されたバージョンよりも新しいバージョンである場合にのみ、データベースが返されます。

**例** 
 
```kusto
.show database TestDB schema as json
```

**出力**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>。データベーススキーマを csl スクリプトとして表示します

指定された (または現在の) データベーススキーマのコピーを作成するために必要なすべてのコマンドを使用して、CSL スクリプトを生成します。

**構文**

`.show``database`  `schema` DatabaseName `as``csl` `script` [ `with(` *オプション* `)` ]

**引数**

次の *オプション* はすべて省略可能です。

* `IncludeEncodingPolicies`: ( `true`  |  `false` )-の場合 `true` 、データベース/テーブル/列レベルでのエンコードポリシーが含まれます。 既定値は、`false` です。 
* `IncludeSecuritySettings`: ( `true`  |  `false` )-既定値 `false` はです。 の場合 `true` 、次のオプションが含まれます。
  * データベース/テーブルレベルで承認されたプリンシパル。
  * テーブルレベルの行レベルのセキュリティポリシー。
  * テーブルレベルでの制限付きビューアクセスポリシー。
* `IncludeIngestionMappings`: ( `true`  |  `false` )-の場合 `true` 、テーブルレベルでのインジェストマッピングが含まれます。 既定値は、`false` です。 

**戻り値**

文字列として返されるスクリプトには、次のものが含まれます。

* コマンドを作成して、データベース内のすべてのテーブルを作成します。
* すべてのデータベース/テーブル/列ポリシーを元のポリシーと一致するように設定するコマンド。
* データベース内のすべてのユーザー定義関数を作成または変更するコマンド。

**例** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
