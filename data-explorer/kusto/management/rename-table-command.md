---
title: . テーブル名およびテーブル名の変更-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのテーブル名の変更とテーブルの名前変更の方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a1644021d1e2df294782d3b24e111d3c57af5f91
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617580"
---
# <a name="rename-table-and-rename-tables"></a>. テーブル名およびテーブル名を変更します。

既存のテーブルの名前を変更します。

この`.rename tables`コマンドは、データベース内の複数のテーブルの名前を1つのトランザクションとして変更します。

[Database admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.rename``table` *OldName* OldName `to` *NewName*

`.rename``tables` *NewName*NewName = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName*既存のテーブルの名前を指定します。 が指定されていない限り、 *OldName*で既存のテーブルに名前が付いていない場合、エラー `ifexists`が発生し、コマンド全体が失敗します (影響はありません)。ただし、を指定しない場合、[名前の変更] コマンドのこの部分は無視されます。
> * *NewName*は、 *OldName*を呼び出すために使用された既存のテーブルの新しい名前です。
> * を`ifexists`指定した場合、存在しないテーブルの部分の名前変更を無視するようにコマンドの動作を変更します。

**解説**

このコマンドは、スコープ内のデータベースのテーブルに対してのみ動作します。
クラスター名またはデータベース名を使用してテーブル名を修飾することはできません。

このコマンドでは、新しいテーブルを作成したり、既存のテーブルを削除したりすることはできません。
コマンドによって記述された変換は、データベース内のテーブルの数が変更されないようにする必要があります。

このコマンドでは、上記の規則に従っている限り、テーブル名またはより複雑な順列のスワップ**が**サポートされています。 たとえば、複数のステージングテーブルにデータを取り込み、1つのトランザクションで既存のテーブルとスワップします。

**使用例**

たとえば`A`、 `B`、、 `C`、および`A_TEMP`の各テーブルを含むデータベースがあるとします。
次のコマンドを実行`A`する`A_TEMP`と、と ( `A_TEMP`テーブルが`A`呼び出され、もう1つの方法で) の名前`B`が`NEWB`変更され`C` 、そのまま保持されるようになります。 

```kusto
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

次の一連のコマンドを実行します。
1. 新しい一時テーブルを作成します。
1. 既存のテーブルまたは既存でないテーブルを新しいテーブルに置き換えます。

```kusto
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```
