---
title: クエリ V2 HTTP 応答-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Query V2 HTTP 応答について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 86a56d77005b2c6b5c9d38bbec85eebfbcb481dc
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617903"
---
# <a name="query-v2-http-response"></a>クエリ V2 HTTP 応答

ステータスコードが200の場合、応答本文は JSON 配列になります。
配列内の各 JSON オブジェクトを_フレーム_と呼びます。

フレームにはいくつかの種類があります。

* [DataSetHeader](#datasetheader)
* [TableHeader](#tableheader)
* [TableFragment](#tablefragment)
* [TableProgress](#tableprogress)
* [TableCompletion](#tablecompletion)
* [データ](#datatable)
* [DataSetCompletion](#datasetcompletion)

## <a name="datasetheader"></a>DataSetHeader 

`DataSetHeader`フレームは常にデータセット内の最初のものであり、1回だけ表示されます。

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

各値の説明:

* `Version`はプロトコルのバージョンです。 現行バージョンは `v2.0`です。
* `IsProgressive`このデータセットにプログレッシブフレームが含まれているかどうかを示すブール型のフラグです。 
   プログレッシブフレームは次のいずれかです。
   
     | フレーム             | 説明                                    |
     |-------------------| -----------------------------------------------|
     | `TableHeader`     | テーブルに関する一般的な情報が含まれます。   |
     | `TableFragment`   | テーブルの四角形のデータシャードが含まれます。 |
     | `TableProgress`   | 進行状況をパーセント (0-100) で示します。       |
     | `TableCompletion` | このフレームが最後のフレームであることを示します。      |
        
    上のフレームには、テーブルが記述されています。
    フラグが`IsProgressive` true に設定されていない場合、セット内のすべてのテーブルが1つのフレームを使用してシリアル化されます。
* `DataTable`: データセット内の1つのテーブルに関してクライアントに必要なすべての情報が含まれています。

## <a name="tableheader"></a>TableHeader

オプションを true に設定し`results_progressive_enabled`て作成されたクエリには、このフレームを含めることができます。 次の表に従って、クライアントはと`TableFragment` `TableProgress`フレームのインターリーブシーケンスを想定できます。 テーブルの最終フレームは`TableCompletion`です。

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

各値の説明:

* `TableId`テーブルの一意の ID を示します。
* `TableKind`次のいずれか:

    * PrimaryResult
    * Queryの情報
    * QueryTraceLog
    * QueryPerfLog
    * TableOfContents
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`テーブルの名前を指定します。
* `Columns`は、テーブルのスキーマを記述する配列です。

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

サポートされる列の型については、[こちら](../../query/scalar-data-types/index.md)を参照してください。

## <a name="tablefragment"></a>TableFragment

この`TableFragment`フレームには、テーブルの四角形のデータフラグメントが含まれています。 このフレームには、実際のデータに加えて、 `TableFragmentType`フラグメントの処理方法をクライアントに指示するプロパティも含まれています。 フラグメントは、既存のフラグメントに追加されるか、置き換えられます。

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

各値の説明:

* `TableId`テーブルの一意の ID を示します。
* `FieldCount`テーブル内の列の数を指定します。
* `TableFragmentType`このフラグメントでクライアントが行う処理について説明します。 
    `TableFragmentType`次のいずれか:
    
    * DataAppend
    * DataReplace
      
* `Rows`は、フラグメントデータを格納する2次元配列です。

## <a name="tableprogress"></a>TableProgress

`TableProgress`フレームは、上で説明`TableFragment`したフレームとインターリーブできます。
その唯一の目的は、クエリの進行状況をクライアントに通知することです。

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

各値の説明:

* `TableId`テーブルの一意の ID を示します。
* `TableProgress`進行状況をパーセント (0--100) で示します。

## <a name="tablecompletion"></a>TableCompletion

フレーム`TableCompletion`は、テーブル転送の終了を示します。 そのテーブルに関連するフレームは送信されません。

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

各値の説明:

* `TableId`テーブルの一意の ID を示します。
* `RowCount`テーブル内の行の合計数を指定します。

## <a name="datatable"></a>DataTable

フラグを false に設定し`EnableProgressiveQuery`て発行されたクエリには、フレーム (`TableHeader`、 `TableFragment`、 `TableProgress`、および`TableCompletion`) は含まれません。 代わりに、データセット内の各テーブルは、クライアントが必要`DataTable`とするすべての情報が含まれているフレームを使用して送信され、テーブルを読み取ることができます。

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
    "Rows": Array,
}
```    

各値の説明:

* `TableId`テーブルの一意の ID を示します。
* `TableKind`次のいずれか:

    * PrimaryResult
    * Queryの情報
    * QueryTraceLog
    * QueryPerfLog
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`テーブルの名前を指定します。
* `Columns`はテーブルのスキーマを記述する配列で、次のものが含まれます。

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

* `Rows`は、テーブルのデータを格納する2次元配列です。

### <a name="the-meaning-of-tables-in-the-response"></a>応答内のテーブルの意味

* `PrimaryResult`-クエリのメインの表形式の結果。 各テーブル[式ステートメント](../../query/tabularexpressionstatements.md)では、ステートメントによって生成される結果を表す1つ以上のテーブルが順に生成されます。 [バッチ](../../query/batches.md)および[フォーク演算子](../../query/forkoperator.md)によって、このようなテーブルが複数存在する場合があります。
* `QueryCompletionInformation`-クエリ自体の実行に関する追加情報 (正常に完了したかどうかなど) と、クエリで使用されたリソース (v1 応答の QueryStatus テーブルに似ています) を提供します。 
* `QueryProperties`-クライアントの視覚化の手順 (たとえば、 [render 操作](../../query/renderoperator.md)の情報を反映するために生成された) や[データベースのカーソル](../../management/databasecursor.md)情報などの追加の値を提供します。
* `QueryTraceLog`-パフォーマンストレースログ情報 ([ `perftrace` [クライアント要求のプロパティ](../netfx/request-properties.md)] が [true] に設定されている場合に返されます)。

## <a name="datasetcompletion"></a>DataSetCompletion

`DataSetCompletion`フレームは、データセット内の最後のフレームです。

```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

各値の説明:

* `HasErrors`データセットの生成中にエラーが発生した場合は true です。
* `Cancelled`データセットの生成の原因となった要求が、完了前にキャンセルされた場合は true です。 
* `OneApiErrors`が true の場合`HasErrors`にのみ返されます。 形式の説明については、「7.10.2」セクションを参照してください。 [here](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md) `OneApiErrors`
