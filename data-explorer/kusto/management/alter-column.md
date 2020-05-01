---
title: . alter 列-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの alter column について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: ecf0fa09438f8df5792d8826150d58f06360cace
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617869"
---
# <a name="alter-column"></a>.alter column

既存のテーブル列のデータ型を変更します。

> [!WARNING]
> 列のデータ型を変更すると、新しいデータ型ではないその列の既存のデータは、今後のクエリでは無視され、 [null 値](../query/scalar-data-types/null-values.md)に置き換えられます。 を使用`alter column`した後で、別のコマンドを使用して列の型を前の値に戻すこともできますが、そのデータを回復することはできません。
> データを失うことなく列の型を変更するための推奨手順については、[以下](#changing-column-type-without-data-loss)を参照してください。

**構文** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* ColumnName `type` *ColumnNewType* columnnewtype `=`
 
**例** 

```kusto
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>データ損失を伴わない列の型の変更

履歴データを保持したまま列の型を変更するには、適切に型指定された新しいテーブルを作成します。

列の型`T1`を変更する各テーブルについて、次の手順を実行します。

1. 正しいスキーマ ( `T1_prime`適切な列の型) を使用してテーブルを作成します。
1. [テーブル名の変更] コマンドを使用してテーブルを交換し[ます。](rename-table-command.md)これにより、テーブル名を交換できます。

```kusto
.rename tables T_prime=T1, T1=T_prime
```

コマンドが完了すると、へ`T1`の新しいデータフローが正しく入力され、履歴データがで`T1_prime`使用できるようになります。

データ`T1_prime`が保持期間外になるまで、クエリは`T1` 、との`T1_prime`和集合を実行するために変更する必要があります。