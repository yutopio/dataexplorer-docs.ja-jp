---
title: Kusto streaming インジェスト policy management-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのストリーミングインジェストポリシーの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1c3ce0c0d383d07375333b08de336503d1578b1a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83381997"
---
# <a name="streaming-ingestion-policy-management"></a>ストリーミングインジェストポリシーの管理

ストリーミングインジェストポリシーをデータベースまたはテーブルにアタッチして、それらの場所へのストリーミングの取り込みを可能にすることができます。 このポリシーでは、ストリーミングインジェストに使用される行ストアも定義します。

ストリーミングインジェストの詳細については、[ストリーミングインジェスト (プレビュー)](../../ingest-data-streaming.md)に関する説明を参照してください。 ストリーミングインジェストポリシーの詳細については、[ストリーミングインジェストポリシー](streamingingestionpolicy.md)に関するページを参照してください。

## <a name="show-policy-streamingingestion"></a>。ポリシー streamingingestion を表示します。

コマンドは、 `.show policy streamingingestion` データベースまたはテーブルのストリーミングインジェストポリシーを表示します。

**構文**

`.show``database` `policy` `streamingingestion` 
 MyDatabase `.show``table`MyTable `policy``streamingingestion`

**戻り値**

このコマンドは、次の列を含むテーブルを返します。

|Column    |種類    |説明
|---|---|---
|PolicyName|`string`|ポリシー名-StreamingIngestionPolicy
|EntityName|`string`|データベース名またはテーブル名
|ポリシー    |`string`|ストリーミングインジェストポリシー[オブジェクト](#streaming-ingestion-policy-object)として書式設定されたストリーミングインジェストポリシーを定義する JSON オブジェクト

**例**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|ポリシー|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"NumberOfRowStores": 4}

### <a name="streaming-ingestion-policy-object"></a>ストリーミングインジェストポリシーオブジェクト

|プロパティ  |Type    |説明                                                       |
|----------|--------|------------------------------------------------------------------|
|NumberOfRowStores |`int`  |エンティティに割り当てられている行ストアの数|
|SealIntervalLimit|`TimeSpan?`|テーブルに対する封印操作の間隔を指定します (省略可能)。 有効な範囲は 1 ~ 24 時間です。 既定値: 24 時間。|
|SealThresholdBytes|`int?`|テーブルで1つの封印操作に使用するデータ量の制限 (オプション)。 値の有効な範囲は 10 ~ 200 MBs です。 既定値は 200 Mb です。|

## <a name="alter-policy-streamingingestion"></a>. alter ポリシー streamingingestion

この `.alter policy streamingingestion` コマンドは、データベースまたはテーブルの streamingingestion ポリシーを設定します。

**構文**

* `.alter``database` `policy` MyDatabase `streamingingestion`*StreamingIngestionPolicyObject*

* `.alter``database` `policy` MyDatabase `streamingingestion``enable`

* `.alter``database` `policy` MyDatabase `streamingingestion``disable`

* `.alter``table` `policy` MyTable `streamingingestion`*StreamingIngestionPolicyObject*

* `.alter``table` `policy` MyTable `streamingingestion``enable`

* `.alter``table` `policy` MyTable `streamingingestion``disable`

**メモ**

1. *StreamingIngestionPolicyObject*は、ストリーミングインジェストポリシーオブジェクトが定義されている JSON オブジェクトです。

2. `enable`-ポリシーが定義されていない場合、または既に0行ストアで定義されている場合は、ストリーミングインジェストポリシーを4行ストアで設定します。それ以外の場合、コマンドは何も行いません。

3. `disable`-正の行ストアでポリシーが既に定義されている場合は、ストリーミングインジェストポリシーを0行ストアに設定します。それ以外の場合、コマンドは何も行いません。

**例**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>。削除ポリシー streamingingestion

コマンドは、 `.delete policy streamingingestion` データベースまたはテーブルから streamingingestion ポリシーを削除します。

**構文** 

`.delete``database`MyDatabase `policy``streamingingestion`

`.delete``table`MyTable `policy``streamingingestion`

**戻り値**

このコマンドは、テーブルまたはデータベースの streamingingestion ポリシーオブジェクトを削除し、対応する[show policy streamingingestion](#show-policy-streamingingestion)コマンドの出力を返します。

**例**

```kusto
.delete database MyDatabase policy streamingingestion 
```