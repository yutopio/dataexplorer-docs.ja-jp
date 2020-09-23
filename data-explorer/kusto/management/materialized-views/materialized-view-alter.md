---
title: 具体化したビューの変更-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで具体化されたビューを変更する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: ac9fb575f46bb60e313da4fa2b3c023ac826daec
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057269"
---
# <a name="alter-materialized-view"></a>. 変更を具体化したビュー

[具体化](materialized-view-overview.md)されたビューを変更すると、そのビューの既存のデータを保持したまま、具体化されたビューのクエリを変更することができます。

具体化されたビューを変更するには、 [データベース管理者](../access-control/role-based-authorization.md) のアクセス許可、または具体化されたビューの管理者が必要です。 詳細については、「 [セキュリティロールの管理](../security-roles.md)」を参照してください。

> [!WARNING]
> 具体化されたビューを変更する場合は、余分な注意が必要です。 不適切な使用により、データが失われる可能性があります。

## <a name="syntax"></a>構文

`.alter` `materialized-view`  
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]  
*ViewName* `on table`*Sourcetablename*  
`{`  
    &nbsp;&nbsp;&nbsp;&nbsp;*照会*  
`}`

## <a name="arguments"></a>引数

|引数|種類|説明
|----------------|-------|---|
|ViewName|String|具体化したビューの名前。|
|Targettablename|String|ビューが定義されているソーステーブルの名前。|
|クエリ|String|具体化されたビュークエリ。|

## <a name="properties"></a>Properties

`dimensionTables`は、具体化されたビューの alter コマンドで唯一サポートされているプロパティです。 クエリがディメンションテーブルを参照する場合は、このプロパティを使用する必要があります。 詳細については、「. 具体化された [ビューを作成する](materialized-view-create.md) 」を参照してください。

## <a name="use-cases"></a>ユース ケース

* ビューに集計を追加します。たとえば、 `avg` ビュークエリをに変更して、集計をに追加し `T | summarize count(), min(Value) by Id` `T | summarize count(), min(Value), avg(Value) by Id` ます。
* 集計演算子以外の演算子を変更します。 Tor の例では、をに変更して一部のレコードを除外し  `T | summarize arg_max(Timestamp, *) by User` `T | where User != 'someone' | summarize arg_max(Timestamp, *) by User` ます。
* ソーステーブルが変更されたため、クエリを変更せずに Alter を実行します。 Tor の例では、に設定されていないのビューを想定しています `T | summarize arg_max(Timestamp, *) by Id` `autoUpdateSchema` (「 [具体化されたビューを作成](materialized-view-create.md) する」を参照してください)。 ビューのソーステーブルに対して列が追加または削除された場合、ビューは自動的に無効になります。 完全に同じクエリを使用して alter コマンドを実行し、具体化されたビューのスキーマを新しいテーブルスキーマに合わせて変更します。 ビューは、変更後も、[具体化された [ビューを有効に](materialized-view-enable-disable.md) する] コマンドを使用して明示的に有効にする必要があります。

## <a name="alter-materialized-view-limitations"></a>具体化ビューの制限の変更

* **サポートされていない変更:**
    * 列の型の変更はサポートされていません。
    * 列名の変更はサポートされていません。 たとえば、ビューを `T | summarize count() by Id` に変更する `T | summarize Count=count() by Id` と、列が削除され、新しい列が作成されます。この列には `count_` `Count` 、最初に null のみが含まれます。
    * 具体化されたビューグループ式に対する変更はサポートされていません。

* **既存のデータへの影響:**
    * 具体化されたビューを変更しても、既存のデータには影響しません。
    * 新しい列では、既存のすべてのレコードに対して null 値が返されます。その場合、alter コマンドによって取り込まれたのレコードが変更されるまで、null 値が変更
        * たとえば、のビュー `T | summarize count() by bin(Timestamp, 1d)` がに変更された場合、 `T | summarize count(), sum(Value) by bin(Timestamp, 1d)` ビューを `Timestamp=T` 変更する前にレコードが既に処理されている特定のについては、 `sum` 列に部分的なデータが含まれます。 このビューには、alter の実行後に処理されたレコードのみが含まれます。
    * クエリにフィルターを追加しても、既に具体化されているレコードは変更されません。 フィルターは、新しく取り込まれたレコードにのみ適用されます。
