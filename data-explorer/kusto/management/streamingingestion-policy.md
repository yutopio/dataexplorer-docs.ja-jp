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
ms.openlocfilehash: 6f7efe2916bc5c5344bf4a6c4fa4a60bfaa167b7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967351"
---
# <a name="streaming-ingestion-policy-command"></a>ストリーミングインジェストポリシーコマンド

ストリーミングインジェストポリシーをテーブルに設定して、このテーブルへのストリーミングインジェストを許可することができます。 また、データベースレベルでポリシーを設定して、現在のテーブルと将来のテーブルの両方に同じ設定を適用することもできます。

詳細については、「[ストリーミングインジェスト](../../ingest-data-streaming.md)」を参照してください。 ストリーミングインジェストポリシーの詳細については、[ストリーミングインジェストポリシー](streamingingestionpolicy.md)に関するページを参照してください。

## <a name="display-the-policy"></a>ポリシーを表示する

コマンドは、 `.show policy streamingingestion` データベースまたはテーブルのストリーミングインジェストポリシーを表示します。
 
**構文**

`.show``{database|table}` &lt; エンティティ &gt; 名 `policy``streamingingestion`

**戻り値**

このコマンドは、次の列を含むテーブルを返します。

|Column    |種類    |説明
|---|---|---
|PolicyName|`string`|ポリシー名-StreamingIngestionPolicy
|EntityName|`string`|データベース名またはテーブル名
|ポリシー    |`string`|[ストリーミングインジェストポリシーオブジェクト](#streaming-ingestion-policy-object)

**使用例**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|ポリシー|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"IsEnabled": true、"HintAllocatedRate": null}

## <a name="change-the-policy"></a>ポリシーを変更する

`.alter[-merge] policy streamingingestion`コマンドファミリは、データベースまたはテーブルのストリーミングインジェストポリシーを変更するための手段を提供します。

**構文**

* `.alter``{database|table}` &lt; &gt; エンティティ `policy` 名 `streamingingestion``[enable|disable]`

* `.alter``{database|table}` &lt; エンティティ名の &gt; `policy` `streamingingestion` &lt; [ストリーミングインジェストポリシーオブジェクト](#streaming-ingestion-policy-object)&gt;

* `.alter-merge``{database|table}` &lt; エンティティ名の &gt; `policy` `streamingingestion` &lt; [ストリーミングインジェストポリシーオブジェクト](#streaming-ingestion-policy-object)&gt;

> [!Note]
>
> * ポリシーの他のプロパティを変更したり、エンティティでポリシーが定義されていない場合は、プロパティを既定値に設定したりしなくても、ストリーミングインジェストの有効/無効の状態を変更できます。
>
> * エンティティのストリーミングインジェストポリシー全体を置き換えることができます。 [ストリーミングインジェストポリシーオブジェクト](#streaming-ingestion-policy-object)には、すべての必須プロパティが含まれている必要があります。
>
> * エンティティのストリーミングインジェストポリシーの指定したプロパティのみを置き換えることができます。 [ストリーミングインジェストポリシーオブジェクト](#streaming-ingestion-policy-object)には、必須プロパティの一部またはすべてを含めることができます。

**戻り値**

コマンドを実行すると、テーブルまたはデータベースポリシーオブジェクトが変更され、 `streamingingestion` 対応[ `.show policy` `streamingingestion` ](#display-the-policy)するコマンドの出力が返されます。

**使用例**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>ポリシーを削除します。

コマンドは、 `.delete policy streamingingestion` データベースまたはテーブルから streamingingestion ポリシーを削除します。

**構文**

`.delete``{database|table}` &lt; エンティティ &gt; 名 `policy``streamingingestion`

**戻り値**

このコマンドは、テーブルまたはデータベースの streamingingestion ポリシーオブジェクトを削除し、対応する[show policy streamingingestion](#display-the-policy)コマンドの出力を返します。

**使用例**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>ストリーミングインジェストポリシーオブジェクト

ストリーミングインジェストポリシーオブジェクトは、管理コマンドの入力と出力で、次のプロパティを含む JSON 形式の文字列です。

|プロパティ|Type|説明|必須/オプション
|---|---|---|---
|IsEnabled|`bool`|エンティティのストリーミングインジェストが有効になっている| 必須
|HintAllocatedRate|`double`|データはの推定速度 (Gb/時間)|オプション
