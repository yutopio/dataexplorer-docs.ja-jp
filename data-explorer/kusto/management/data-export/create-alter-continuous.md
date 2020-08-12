---
title: 継続的なデータエクスポートの作成または変更-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで連続データエクスポートを作成または変更する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b75ac4fe4b03c85eec74ccc69c5c03ff59a4bcfe
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149378"
---
# <a name="create-or-alter-continuous-export"></a>連続エクスポートの作成または変更

連続エクスポートジョブを作成または変更します。

## <a name="syntax"></a>構文

`.create-or-alter` `continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)` ] <br>
`to``table` *Externaltablename* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *照会*

## <a name="properties"></a>Properties

| プロパティ             | Type     | 説明   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 連続エクスポートの名前。 名前はデータベース内で一意である必要があり、連続エクスポートを定期的に実行するために使用されます。      |
| ExternalTableName    | String   | エクスポート先の[外部テーブル](../externaltables.md)の名前。  |
| クエリ                | String   | エクスポートするクエリ。  |
| over (T1, T2)        | String   | クエリ内のファクトテーブルのコンマ区切りのリスト (省略可能)。 指定しない場合、クエリで参照されるすべてのテーブルがファクトテーブルと見なされます。 指定した場合、この一覧に含まれて*いない*テーブルはディメンションテーブルとして扱われ、スコープは設定されません (すべてのレコードがすべてのエクスポートに参加します)。 詳細については、「[継続的なデータエクスポートの概要](continuous-data-export.md)」を参照してください。 |
| intervalBetweenRuns  | Timespan | 連続エクスポート実行の間隔。 1分以上にする必要があります。   |
| forcedLatency        | Timespan | この期間の前にのみ取り込まれたされたレコードに対してクエリを制限するオプションの期間 (現在の時刻に対して)。 このプロパティは、たとえば、クエリがいくつかの集計/結合を実行し、エクスポートを実行する前に関連するすべてのレコードが既に取り込まれたされていることを確認したい場合に便利です。

上記に加えて、[[外部テーブルへのエクスポート] コマンド](export-data-to-an-external-table.md)でサポートされているすべてのプロパティが、連続エクスポートの create コマンドでサポートされています。 

## <a name="example"></a>例

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| 名前     | ExternalTableName | クエリ | ForcedLatency | IntervalBetweenRuns | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']。[S '] "<br>] | {<br>  "SizeLimit": 104857600<br>} |
