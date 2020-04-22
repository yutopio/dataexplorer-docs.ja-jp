---
title: .create テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .create テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 1c84b9b6cec5a5bb7ab620871f59c188bf71cef4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744237"
---
# <a name="create-table"></a>.create テーブル

新しい空のテーブルを作成します。

コマンドは、特定のデータベースのコンテキストで実行する必要があります。

[データベース ユーザーのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create``table`*テーブル名*([列名:列タイプ]、.) [`with` `(``docstring``=`*ドキュメント*]`,` `folder` `=` [`)`*フォルダ名*] ]

テーブルが既に存在する場合、コマンドは成功を返します。

**例** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**出力を返す**

次の場合と同じ JSON 形式でテーブルのスキーマを返します。

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 複数のテーブルを作成する場合は、クラスターのパフォーマンスを向上させ、負荷を軽減するために[、.create tables](/create-tables.md)コマンドを使用します。

## <a name="create-merge-table"></a>作成-マージ テーブル

新しいテーブルを作成するか、既存のテーブルを拡張します。 

コマンドは、特定のデータベースのコンテキストで実行する必要があります。 

[データベース ユーザーのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create-merge``table`*テーブル名*([列名:列タイプ]、.) [`with` `(``docstring``=`*ドキュメント*]`,` `folder` `=` [`)`*フォルダ名*] ]

テーブルが存在しない場合は、".create table" コマンドとまったく同じように機能します。

テーブル T が存在し、".create-merge テーブル T<columns specification>( )" コマンドを送信した場合は、次のようにします。

* T に<columns specification>以前存在しなかった列は、T のスキーマの末尾に追加されます。
* T の列が存在<columns specification>しない場合は、T から削除されません。
* T に<columns specification>存在する列が、データ型が異なっている場合は、コマンドが失敗します。
