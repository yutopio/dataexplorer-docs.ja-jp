---
title: ストリーミング インジェスト ポリシー管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのストリーミング インジェスト ポリシー管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b0b1a76e52688dcc88ca87023309f9c970b4c702
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519620"
---
# <a name="streaming-ingestion-policy-management"></a>ストリーミングインジェストポリシー管理

ストリーミング インジェスト ポリシーをデータベースまたはテーブルにアタッチして、それらの場所へのストリーミング インジェストを許可できます。 また、このポリシーは、ストリーミング インジェストに使用される行ストアも定義します。

ストリーミング インジェストの詳細については、「[ストリーミング インジェスト (プレビュー)」](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)を参照してください。 ストリーミング インジェスト ポリシーの詳細については、「ストリーミング イン[ジェスト ポリシー](streamingingestionpolicy.md)」を参照してください。

## <a name="show-policy-streamingingestion"></a>.show ポリシーストリーミング

この`.show policy streamingingestion`コマンドは、データベースまたはテーブルのストリーミングインジェストポリシーを表示します。

**構文**

`.show``database`マイ`policy``streamingingestion`データベース
`policy`マイテーブル`.show``table``streamingingestion`

**戻り値**

このコマンドは、次の列を持つテーブルを返します。

|列    |Type    |説明
|---|---|---
|PolicyName|`string`|ポリシー名 - ストリーミングポリシー
|EntityName|`string`|データベースまたはテーブル名
|ポリシー    |`string`|ストリーミング インジェスト ポリシー オブジェクトとして書式設定されたストリーミング[インジェスト ポリシーを定義する](#streaming-ingestion-policy-object)JSON オブジェクト

**例**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|ポリシー|子エンティティ|EntityType|
|---|---|---|---|---|
|ストリーミングインジェストポリシー|DB1|{ "数の行の店": 4 }

### <a name="streaming-ingestion-policy-object"></a>ストリーミング インジェスト ポリシー オブジェクト

|プロパティ  |Type    |説明                                                       |
|----------|--------|------------------------------------------------------------------|
|数の行のストア |`int`  |エンティティに割り当てられた行ストアの数|
|シールインターバルリミット|`TimeSpan?`|テーブル上のシール操作間の間隔のオプションの制限。 有効な範囲は 1 ~ 24 時間です。 既定値: 24 時間。|
|しきい値バイトを封印する|`int?`|テーブル上の単一のシール操作に対して取られるデータ量のオプションの制限。 値の有効な範囲は 10 から 200 MB です。 デフォルト: 200 MB。|

## <a name="alter-policy-streamingingestion"></a>変更ポリシーストリーミング

この`.alter policy streamingingestion`コマンドは、データベースまたはテーブルのストリーミング ポリシーを設定します。

**構文**

* `.alter``database`マイデータベース`policy``streamingingestion`*ストリーミングポリシーオブジェクト*

* `.alter``database`データベース`policy``streamingingestion``enable`

* `.alter``database`データベース`policy``streamingingestion``disable`

* `.alter``table`マイテーブル`policy``streamingingestion`*ストリーミングポリシーオブジェクト*

* `.alter``table`マイテーブル`policy``streamingingestion``enable`

* `.alter``table`マイテーブル`policy``streamingingestion``disable`

**メモ**

1. *ストリーミングインジェストポリシーオブジェクト*は、ストリーミングインジェストポリシーオブジェクトが定義されているJSONオブジェクトです。

2. `enable`- ポリシーが定義されていない場合、または 0 行ストアで既に定義されている場合は、ストリーミング インジェスト ポリシーを 4 つの行ストアに設定します。

3. `disable`- ポリシーが正の行ストアで既に定義されている場合は、ストリーミング インジェスト ポリシーを 0 行ストアに設定します。

**例**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>.delete ポリシーストリーミング

この`.delete policy streamingingestion`コマンドは、データベースまたはテーブルからストリーミングポリシーを削除します。

**構文** 

`.delete``database`データベース`policy``streamingingestion`

`.delete``table`マイテーブル`policy``streamingingestion`

**戻り値**

このコマンドは、テーブルまたはデータベース・ストリーミング・ポリシー・オブジェクトを削除し、対応する[.show ポリシー・ストリーミング・](#show-policy-streamingingestion)コマンドの出力を戻します。

**例**

```kusto
.delete database MyDatabase policy streamingingestion 
```