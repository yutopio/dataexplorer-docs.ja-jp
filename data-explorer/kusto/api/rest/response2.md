---
title: クエリ V2 HTTP 応答 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリ V2 HTTP 応答について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cca9b8381c7c59993c1e9071c46f34c1754d2429
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524312"
---
# <a name="query-v2-http-response"></a>V2 HTTP 応答のクエリ

状態コードが 200 の場合、応答本文は JSON 配列です。
配列内の各 JSON オブジェクトは_フレーム_と呼ばれます。

フレームには7種類あります。

1. ヘッダー
2. テーブルヘッダー
3. テーブルフラグメント
4. テーブルプログレス
5. テーブル補完
6. DataTable
7. データセット完了

## <a name="datasetheader"></a>ヘッダー 

これは常にデータセットの最初のフレームであり、正確に 1 回だけ表示されます。

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

各値の説明:

1. `Version`はプロトコルバージョンを保持します。 現行バージョンは `v2.0`です。
2. `IsProgressive`は、このデータセットにプログレッシブ フレームが含まれているかどうかを示すブールフラグです。 プログレッシブ フレームは、次のいずれかです。
    1. `TableHeader`: テーブルに関する一般的な情報が含まれています。
    2. `TableFragment`: テーブルの直腸データシャードが含まれています
    3. `TableProgress`: 進捗率がパーセント (0 ~ 100) で表示されます。
    4. `TableCompletion`: これがテーブルの最後のフレームであることを示します。
        
    上記の 4 つのフレームは、テーブルを表すために一緒に使用されます。
    このフラグが設定されていない場合、セット内のすべてのテーブルは単一のフレームを使用してシリアル化されます。
      1. `DataTable`: データ セット内の 1 つのテーブルに関してクライアントが必要とするすべての情報が含まれます。


## <a name="tableheader"></a>テーブルヘッダー

オプションを true に設定`results_progressive_enabled`して発行されるクエリには、このフレームが含まれる場合があります。 この表の後に、クライアントはインターリーブシーケンス`TableFragment`と`TableProgress`フレーム、およびフレームが続`TableCompletion`くことを期待する必要があります。 その後、そのテーブルに関連するフレームは送信されません。

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

各値の説明:

1. `TableId`はテーブルの一意の ID です。
2. `TableKind`はテーブルの種類で、次のいずれかになります。

      * プライマリ結果
      * クエリ完了情報
      * クエリトレースログ
      * ログ
      * TableOfContents
      * QueryProperties
      * クエリプラン
      * Unknown
3. `TableName`はテーブルの名前です。
4. `Columns`は、テーブルのスキーマを記述する配列です。

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
サポートされている列の種類については[、ここで](../../query/scalar-data-types/index.md)説明します。

## <a name="tablefragment"></a>テーブルフラグメント

このフレームには、テーブルの長方形のデータフラグメントが含まれています。 実際のデータに加えて、このフレームには`TableFragmentType`、フラグメントをどう処理するかをクライアントに指示するプロパティが含まれています(既存のフラグメントに追加するか、すべて一緒に置き換えることができます)。

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

各値の説明:

1. `TableId`はテーブルの一意の ID です。
2. `FieldCount`テーブルの列数を指定します。
3. `TableFragmentType`は、クライアントがこのフラグメントをどうするかについて説明します。 以下のいずれかを指定できます。
      * データ追加
      * データ置換
4. `Rows`はフラグメントデータを含む2次元配列です。

## <a name="tableprogress"></a>テーブルプログレス

このフレームは、上述の`TableFragment`フレームとインターリーブすることができる。
唯一の目的は、クエリの進行状況をクライアントに通知することです。

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

各値の説明:

1. `TableId`はテーブルの一意の ID です。
2. `TableProgress`はパーセント (0 -100) の進行状況です。

## <a name="tablecompletion"></a>テーブル補完

フレーム`TableCompletion`はテーブル送信の終わりを示します。 そのテーブルに関連するフレームは送信されません。

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

各値の説明:

1. `TableId`はテーブルの一意の ID です。
2. `RowCount`は、テーブルの最終行数です。

## <a name="datatable"></a>DataTable

`EnableProgressiveQuery`フラグを false に設定して発行されたクエリには、前の 4 つのフレーム`TableHeader`( `TableFragment` `TableProgress` 、、および`TableCompletion`) は含まれません。 代わりに、データ セット内の各テーブルは、単一の`DataTable`フレーム、フレームを使用して送信されます。

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

1. `TableId`はテーブルの一意の ID です。
2. `TableKind`はテーブルの種類で、次のいずれかになります。

      * プライマリ結果
      * クエリ完了情報
      * クエリトレースログ
      * ログ
      * QueryProperties
      * クエリプラン
      * Unknown
3. `TableName`はテーブルの名前です。
4. `Columns`は、テーブルのスキーマを記述する配列です。

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
4. `Rows`は、テーブルのデータを格納する 2 次元配列です。

### <a name="the-meaning-of-tables-in-the-response"></a>応答内のテーブルの意味

* `PrimaryResult`- クエリの表形式の主な結果。 [各表形式の式ステートメント](../../query/tabularexpressionstatements.md)に対して、ステートメントによって生成された結果を表す 1 つ以上のテーブルが順番に出力されます ([バッチ](../../query/batches.md)演算子と[fork 演算子](../../query/forkoperator.md)により、このようなテーブルが複数存在する場合があります ) 。
* `QueryCompletionInformation`- クエリが正常に完了したかどうか、クエリで消費されたリソース (v1 応答の QueryStatus テーブルと同様) など、クエリ自体の実行に関する追加情報を提供します。 
* `QueryProperties`- クライアントの視覚化命令 ([たとえば、render オペレータ](../../query/renderoperator.md)の情報を反映するために出力される) や[データベース カーソル](../../management/databasecursor.md)情報などの追加の値を提供します。
* `QueryTraceLog`- パフォーマンス トレース ログ情報 ([クライアント要求プロパティ](../netfx/request-properties.md)で perftrace を設定するときに返されます)。

## <a name="datasetcompletion"></a>データセット完了

これは、データセットの最後のフレームです。
```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

各値の説明:

1. `HasErrors`は、データ・セットを生成するエラーがあった場合は true です。
2. `Cancelled`データ・セットの生成につながる要求が途中で取り消された場合は true です。 
3. `OneApiErrors`は、true の`HasErrors`場合にのみ送信されます。 `OneApiErrors`フォーマットの詳細については、セクション 7.10.2[を参照してください](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)。