---
title: インジェスト into コマンド (ストレージからデータをプル)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのインジェスト into コマンド (ストレージからのデータのプル) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 05d62aaa7b123f7f6d02b784402fd06335e155b2
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373423"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>インジェスト into コマンド (ストレージからデータをプル)

この `.ingest into` コマンドは、1つまたは複数のクラウドストレージアーティファクトからデータを "プル" することによって、データをテーブルに取り込みします。
たとえば、このコマンドは、Azure Blob Storage から CSV 形式の 1000 blob を取得して解析し、1つのターゲットテーブルにまとめて取り込むことができます。
テーブルには、既存のレコードに影響を与えることなく、テーブルのスキーマを変更せずに、データが追加されます。

**構文**

`.ingest`[ `async` ] `into` `table` *TableName* *SourceDataLocator* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ]

**引数**

* `async`: 指定した場合、コマンドはすぐに制御を戻し、バックグラウンドで取り込みを続行します。 コマンドの結果には、インジェストの `OperationId` `.show operation` 完了状態と結果を取得するためにコマンドで使用できる値が含まれます。
  
* *TableName*: データの取り込み先となるテーブルの名前。
  テーブル名は常にデータベースに対する相対参照であり、スキーママッピングオブジェクトが指定されていない場合は、そのスキーマがデータに対して想定されるスキーマです。

* *SourceDataLocator*: 型のリテラル、 `string` またはと文字で囲まれたそのようなリテラルのコンマ区切りのリスト。 `(` `)` プルするデータを含むストレージアーティファクトを示します。 「[ストレージ接続文字列](../../api/connection-strings/storage.md)」を参照してください。

> [!NOTE]
> *SourceDataPointer*には、実際の資格情報を含む、難読化された[文字列リテラル](../../query/scalar-data-types/string.md#obfuscated-string-literals)を使用することを強くお勧めします。
> サービスは、内部トレース、エラーメッセージなどの資格情報を確実にスクラブします。

* *IngestionPropertyName*, *IngestionPropertyValue*: インジェストプロセスに影響を与える任意の数の[インジェストプロパティ](../../../ingestion-properties.md)。

**結果**

コマンドの結果は、コマンドによって生成されるデータシャード ("エクステント") の数と同数のレコードを含むテーブルです。
データシャードが生成されていない場合は、空の (ゼロ値) エクステント ID で1つのレコードが返されます。

|名前       |Type      |説明                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |コマンドによって生成されたデータシャードの一意の識別子。|
|ItemLoaded |`string`  |このレコードに関連する1つ以上のストレージアーティファクト。             |
|Duration   |`timespan`|インジェストの実行にかかった時間。                                     |
|HasErrors  |`bool`    |このレコードがインジェストの失敗を表すかどうかを示します。                |
|OperationId|`guid`    |操作を表す一意の ID。 コマンドと共に使用でき `.show operation` ます。|

**解説**

このコマンドでは、取り込まれたするテーブルのスキーマは変更されません。
必要に応じて、データは取り込み中にこのスキーマに "強制" されます。これに対して、他の方法ではありません (余分な列は無視され、欠落している列は null 値として扱われます)。

**使用例**

次の例では、Azure Blob Storage から CSV ファイルとして2つの blob を読み取り、その内容をテーブルに取り込むようにエンジンに指示し `T` ます。 は、 `...` 各 blob への読み取りアクセスを提供する Azure Storage shared access signature (SAS) を表します。 また、(文字列値の前にある) 難読化された文字列を使用して、 `h` SAS が記録されないように注意してください。

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

次の例は、Azure Data Lake Storage Gen 2 (ADLSv2) の取り込みデータを対象としています。 ここで使用する資格情報 ( `...` ) は、ストレージアカウントの資格情報 (共有キー) であり、接続文字列のシークレット部分に対してのみ文字列難読化を使用します。

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

次の例では、Azure Data Lake Storage (ADLS) から1つのファイルを取り込みします。
ユーザーの資格情報を使用して ADLS にアクセスします (そのため、シークレットを含むストレージ URI を処理する必要はありません)。 インジェストプロパティを指定する方法についても説明します。

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

