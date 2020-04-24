---
title: .rename テーブルと .rename テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この資料では、Azure データ エクスプローラーのテーブルの名前の変更と名前の変更について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: db3e38c562573f52df70979b71fe72d8947158bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520487"
---
# <a name="rename-table-and-rename-tables"></a>.rename テーブルと .rename テーブル

既存のテーブルの名前を変更します。

この`.rename tables`コマンドは、データベース内の多数のテーブルの名前を単一のトランザクションとして変更します。

[データベース管理者のアクセス許可が](../management/access-control/role-based-authorization.md)必要です。

**構文**

`.rename``table`*古い名前*`to`*の新しい名前*

`.rename``tables`*新しい名前* = *の古い名前*[`ifexists`] [`,` ..

> [!NOTE]
> * *OldName*は既存のテーブルの名前です。 *OldName*が指定されていない場合`ifexists`(この場合、rename コマンドのこの部分は無視されます)、既存のテーブルに名前を付けていない場合は、エラーが発生し、コマンド全体が失敗します (効果はありません)。
> * *新しい名前*は、以前は OldName と呼ばれていた既存のテーブルの新しい*名前です*。
> * 指定`ifexists`すると、存在しない表の名前変更部分を無視するようにコマンドの動作が変更されます。

**解説**

このコマンドは、スコープ内のデータベースのテーブルに対してのみ動作します。
テーブル名は、クラスター名またはデータベース名で修飾することはできません。

このコマンドは、新しいテーブルを作成したり、既存のテーブルを削除したりしません。
コマンドによって記述される変換は、データベース内のテーブルの数が変わらないようにする必要があります。

このコマンド**は、** 上記の規則に従っている限り、テーブル名のスワッピング、または複雑な順列をサポートします。 たとえば、複数のステージング テーブルにデータを取り込み、1 つのトランザクションで既存のテーブルと交換します。

**使用例**

データベースに、次のテーブル`A`、、`B`および`C`を`A_TEMP`示すデータベースを想像してみてください。
次のコマンドは、`A`スワップ`A_TEMP`され`A_TEMP`、(テーブルが 呼び出`A`され、その逆の方向に)、`NEWB`に名前を`C`変更`B`し、そのままにします。 

```
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

次の一連のコマンド
1. 新しい一時テーブルを作成します。
1. 既存のテーブルまたは既存でないテーブルを新しいテーブルに置き換えます。

```
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```