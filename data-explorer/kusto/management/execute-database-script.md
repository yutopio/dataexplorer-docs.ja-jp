---
title: . データベーススクリプトの実行-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのデータベーススクリプトの実行機能について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: 4eaa7e8cc6f3c0f321abb9744bfe1608521e7b0e
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784533"
---
# <a name="execute-database-script"></a>.execute database script

1つのデータベースのスコープ内で制御コマンドのバッチを実行します。

## <a name="syntax"></a>構文

`.execute` `database` `script`  
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]   
`<|`  
 *制御-コマンド-スクリプト*

### <a name="parameters"></a>パラメーター

* *Control コマンド-script*: 1 つ以上のコントロールコマンドを含むテキスト。
* *データベーススコープ*: スクリプトは、要求コンテキストの一部として指定された*データベーススコープ*に適用されます。

### <a name="optional-properties"></a>省略可能なプロパティ

| プロパティ            | Type            | 説明                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `ContinueOnErrors`            | `bool`        | に設定する `false` と、スクリプトは最初のエラーで停止します。 に設定する `true` と、スクリプトの実行は続行されます。 既定値: `false`。 |

## <a name="output"></a>出力

スクリプトに表示される各コマンドは、出力テーブルに個別のレコードとして報告されます。 各レコードには、次のフィールドがあります。

|出力パラメーター |Type |説明
|---|---|--- 
|OperationId  |GUID |コマンドの識別子。
|CommandType  |String |コマンドの種類。
|CommandText  |String |特定のコマンドのテキスト。
|結果|String|特定のコマンドの実行結果。
|理由|String|コマンドの実行結果に関する詳細情報。

>[!NOTE]
>* スクリプトテキストには、空の行とコマンド間のコメントが含まれる場合があります。
>* コマンドは、入力スクリプトに表示される順序で順番に実行されます。
>* スクリプトの実行は非トランザクションであり、エラー発生時にロールバックは実行されません。 を使用する場合は、べき等形式のコマンドを使用することをお勧めし `.execute database script` ます。
>* コマンドの既定の動作は、最初のエラーが発生したときに失敗します。これは、プロパティ引数を使用して変更できます。
>* 読み取り専用の制御コマンド ([コマンドの表示]) は実行されず、状態と共に報告され `Skipped` ます。

## <a name="example"></a>例

```kusto
.execute database script <|

// Create tables
.create-merge table T(a:string, b:string)

// Apply policies
.alter-merge table T policy retention softdelete = 10d 

// Create functions
.create-or-alter function
  with (skipvalidation = "true") 
  SampleT1(myLimit: long) { 
    T1 | limit myLimit
}
```

|OperationId|CommandType|CommandText|結果|理由|
|---|---|---|---|---|
|1d28531b-58c8-4023-a5d3-16fa73c06cfa|TableCreate|. create-merge table T (a: string, b: string)|完了||
|67d0ea69-baa4-419a-93d3-234c03834360|RetentionPolicyAlter|. alter-マージテーブル T ポリシー保持ソフト delete = now-10d|完了||
|0b0e8769-d4e8-4ff9-adae-071e52a650c7|FunctionCreateOrAlter|.create-or-alter function<br>with (skipvalidation = "true")<br>SampleT1 (myLimit: long) {<br>T1 \| 制限 myLimit<br>}|完了||
