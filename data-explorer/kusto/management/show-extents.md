---
title: 。エクステントを表示する-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの [エクステントの表示] コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: c0e2ffa76becc747b03b907dfe8a356e4c1a49a3
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060710"
---
# <a name="show-extents"></a>。エクステントを表示します

指定されたデータベースまたはテーブルのエクステントを表示します。

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

## <a name="cluster-level"></a>クラスターレベル

`.show` `cluster` `extents` [`hot`]

クラスター内に存在するエクステント (データシャード) に関する情報を表示します。
`hot`を指定した場合-ホットキャッシュ内に存在することが予想されるエクステントのみが表示されます。

## <a name="database-level"></a>データベース レベル

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,`*Extentidn* `)` ][ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

指定されたデータベースに存在するエクステント (データシャード) に関する情報を表示します。
`hot`が指定されている場合-ホットキャッシュ内に存在することが予想されるエクステントのみを表示します。

## <a name="table-level"></a>テーブルレベル

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,`*Extentidn* `)` ][ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,`*TableNameN* `)``extents`[ `(` *ExtentId1* `,` ... `,`*Extentidn* `)` ][ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

指定したテーブルに存在するエクステント (データシャード) に関する情報を表示します。 データベースはコマンドのコンテキストから取得されます。
を `hot` 指定した場合は、ホットキャッシュ内に存在することが予想されるエクステントのみが表示されます。

## <a name="notes"></a>ノート

* データベースまたはテーブルレベルのコマンドの使用は `| where DatabaseName == '...' and TableName == '...'` 、クラスターレベルのコマンドの結果をフィルター処理する (追加する) よりもはるかに高速です。
* エクステント Id のオプションのリストが指定されている場合、返されるデータセットはそれらのエクステントのみに制限されます。
    * このメソッドは `| where ExtentId in(...)` 、"ベア" コマンドの結果をフィルター処理する (に追加する) よりもはるかに高速です。
* `tags`フィルターが指定されている場合:
    * 返される一覧は、タグコレクションが、指定された*すべて*のタグフィルターに従うエクステントに限定されます。
    * このメソッドは `| where Tags has '...' and Tags contains '...'` 、"ベア" コマンドの結果をフィルター処理する (に追加する) よりもはるかに高速です。
    * `has`フィルターは等値フィルターです。 指定されたいずれかのタグでタグ付けされていないエクステントはフィルターで除外されます。
    * `!has`フィルターは等価の負のフィルターです。 指定されたタグのいずれかでタグ付けされたエクステントはフィルターで除外されます。
    * `contains`フィルターは大文字と小文字を区別しない部分文字列フィルターです。 指定された文字列がタグのいずれかの部分文字列として含まれていないエクステントは、フィルターで除外されます。
    * `!contains`フィルターは、大文字と小文字を区別しない部分文字列の否定フィルターです。 指定された文字列がいずれかのタグの部分文字列として含まれているエクステントはフィルターで除外されます。
  
## <a name="output-parameters"></a>出力パラメーター

|出力パラメーター |Type |説明 |
|---|---|---|
|ExtentId |Guid |エクステントの ID 
|DatabaseName |String |エクステントが属しているデータベース
|TableName |String |エクステントが属しているテーブル
|MaxCreatedOn |DateTime |エクステントが作成された日時。 マージされたエクステントの場合、ソースエクステント間の最大作成時間
|OriginalSize |Double |エクステントデータの元のサイズ (バイト単位)
|ExtentSize |Double |メモリ内のエクステントのサイズ (圧縮 + インデックス)
|CompressedSize |Double |メモリ内のエクステントデータの圧縮サイズ
|IndexSize |Double |エクステントデータのインデックスサイズ
|ブロック |Long |エクステント内のデータブロックの数
|セグメント |Long |エクステント内のデータセグメントの数
|AssignedDataNodes |String | 非推奨 (空の文字列)
|読み込まれる Datの Des |String |非推奨 (空の文字列)
|ExtentContainerId |String | エクステントが含まれているエクステントコンテナーの ID
|RowCount |Long |エクステント内の行の数
|MinCreatedOn |DateTime |エクステントが作成された日時。 マージされたエクステントの場合、ソースエクステント間での作成時間の最小値
|Tags|String|範囲に対して定義されているタグ (存在する場合)
 
## <a name="examples"></a>例

### <a name="tagged-extent"></a>タグ付きエクステント

`E`テーブル内のエクステントに `T` は、タグ、、およびタグが付いてい `aaa` `BBB` `ccc` ます。

* このクエリは次を返し `E` ます。
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* このクエリは、タグ付けされ `E` ていないため、を返しません `aa` 。
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* このクエリは次を返し `E` ます。
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### <a name="show-volume-of-extents-created"></a>作成されたエクステントのボリュームを表示する

1時間あたりに作成されるエクステントの数を特定のデータベースに表示する

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### <a name="show-volume-of-data-arriving-by-table-per-hour"></a>1時間あたりのテーブル別に到着したデータの量を表示する

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### <a name="show-data-size-distribution-by-table"></a>[テーブルごとのデータサイズ分布を表示する]

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### <a name="show-all-extents-in-the-database-named-gamesdb"></a>' GamesDB ' という名前のデータベース内のすべてのエクステントを表示します

```kusto 
.show database GamesDB extents
```

### <a name="show-all-extents-in-the-table-named-games"></a>' Games ' という名前のテーブル内のすべてのエクステントを表示します

```kusto 
.show table Games extents
```

### <a name="show-all-extents-in-specific-tables"></a>特定のテーブル内のすべてのエクステントを表示する

' TaggingGames1 ' と ' TaggingGames2 ' という名前のテーブル内のすべてのエクステントを表示します。 ' tag1 ' と ' tag2 ' の両方でタグが付けられます

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```
