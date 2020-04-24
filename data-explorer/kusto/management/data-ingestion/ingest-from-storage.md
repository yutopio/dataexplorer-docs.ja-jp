---
title: コマンドへの .ingest (ストレージからデータをプル) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .ingest into コマンド (ストレージからデータを取得) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1f304f9dc064094c6d55cb32f3fb453f32114979
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521439"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>コマンドへの .ingest (ストレージからデータをプル)

この`.ingest into`コマンドは、1 つ以上のクラウド ストレージ アーティファクトからデータを "プル" することによって、データをテーブルに取り込みます。
たとえば、コマンドは、Azure BLOB ストレージから 1000 個の CSV 形式の BLOB を取得し、解析して、それらを 1 つのターゲット テーブルにまとめて取得できます。
既存のレコードに影響を与えず、テーブルのスキーマを変更することなく、データがテーブルに追加されます。

**構文**

`.ingest`[`async` `into` `table` ]*テーブル名**ソースデータロケーター* [`with` `(` *インジェスティションプロパティ名*`=`*取得プロパティ値*[`,` .]`)`]

**引数**

* `async`: 指定すると、コマンドはすぐに戻り、バックグラウンドでインジェクションを続行します。 コマンドの結果には、取り込`OperationId`み完了ステータスと結果を`.show operation`取得するためにコマンドとともに使用できる値が含まれます。
  
* *テーブル名*: データを取り込むテーブルの名前。
  テーブル名は常にコンテキスト内のデータベースに対して相対的であり、スキーマ マッピング オブジェクトが指定されていない場合に、そのスキーマはデータに対して想定されるスキーマです。

* *SourceDataLocator*: 型`string`のリテラル、または文字で`(``)`囲まれたリテラルのコンマ区切りのリストで、プルするデータを含むストレージ アーティファクトを示します。 [ストレージ接続文字列](../../api/connection-strings/storage.md)を参照してください。

> [!NOTE]
> 実際の資格情報を含む*SourceDataPointer*には[、難読化された文字列リテラル](../../query/scalar-data-types/string.md#obfuscated-string-literals)を使用することを強くお勧めします。
> サービスは、内部トレース、エラー メッセージなどで資格情報をスクラブすることを確認します。

* *インジェスティションプロパティ名*、*インジェスティションプロパティ値*:[インジェ](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)スティプロセスに影響を与えるインジェスション プロパティの数。

**結果**

コマンドの結果は、コマンドによって生成されたデータ・シャード (「エクステント」) と同じ数のレコードを持つ表です。
データシャードが生成されていない場合は、空の (ゼロ値の) エクステント ID を持つ単一のレコードが返されます。

|名前       |Type      |説明                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|エクステントID   |`guid`    |コマンドによって生成されたデータ シャードの一意の識別子。|
|アイテムを読み込んだ |`string`  |このレコードに関連する 1 つ以上のストレージ成果物。             |
|Duration   |`timespan`|摂取の実行にかかった時間。                                     |
|エラー  |`bool`    |このレコードが取り込み失敗を表すかどうか。                |
|OperationId|`guid`    |操作を表す一意の ID。 コマンドと共に`.show operation`使用できます。|

**解説**

このコマンドは、取り込まれるテーブルのスキーマを変更しません。
必要に応じて、データは取り込み中にこのスキーマに 「強制変換」され、逆の方法ではありません (余分な列は無視され、欠落列は NULL 値として扱われます)。

**使用例**

次の例では、エンジンに対して、Azure BLOB Storage から 2 つの BLOB を CSV`T`ファイルとして読み取り、その内容をテーブル に取り込む方法を指示します。 は`...`、各 BLOB への読み取りアクセスを提供する Azure Storage 共有アクセス署名 (SAS) を表します。 また、SAS が記録されないように、難読化された`h`文字列 (文字列値の前) を使用することにも注意してください。

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

次の例は、Azure データ レイク ストレージ Gen 2 (ADLSv2) からデータを取り込む場合です。 ここで使用される資格情報 (`...`) はストレージ アカウントの資格情報 (共有キー) であり、接続文字列の秘密部分に対してのみ文字列難読化を使用します。

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

次の例では、Azure データ レイク ストレージ (ADLS) から 1 つのファイルを取り込みます。
ユーザーの資格情報を使用して ADLS にアクセスします (そのため、ストレージ URI をシークレットを含むものとして扱う必要はありません)。 また、インジェスト プロパティを指定する方法も示します。

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

