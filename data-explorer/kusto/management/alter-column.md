---
title: .alter 列 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの .alter 列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d41b4f452125fbfebc319112db244deaca79f37a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522612"
---
# <a name="alter-column"></a>.列の変更

既存のテーブル列のデータ型を変更します。

> [!WARNING]
> 列のデータ型を変更すると、その列の既存のデータのうち、新しいデータ型以外のデータは、将来のクエリでは無視され[、null 値](../query/scalar-data-types/null-values.md)に置き換えられます。 を使用`alter column`した後は、別のコマンドを使用して列の種類を以前の値に戻す場合でも、そのデータを回復することはできません。
> データを失わずに列の種類を変更する場合の推奨手順については[、以下](#changing-column-type-without-data-loss)を参照してください。

**構文** 

`.alter``column` [*データベース名*`.`]*テーブル名*`.`*列名*`type``=`*列の新しい型*
 
**例** 

```
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>データ損失のない列の種類の変更

履歴データを保持したまま列の種類を変更するには、新しい正しく型指定されたテーブルを作成します。

列の種類`T1`を変更するテーブルごとに、次の手順を実行します。

1. 正しいスキーマ`T1_prime`(右側の列の型) を持つテーブルを作成します。
1. テーブル名を入れ替え可能な[.rename tables](rename-table-command.md)コマンドを使用して、テーブルをスワップします。

```
.rename tables T_prime=T1, T1=T_prime
```

コマンドが完了すると、`T1`新しいデータフローが正しく入力され、履歴データが で`T1_prime`使用可能になります。

`T1_prime`データが保持ウィンドウから出るまで、クエリの接触`T1`を変更して、 と`T1_prime`のユニオンを実行する必要があります。