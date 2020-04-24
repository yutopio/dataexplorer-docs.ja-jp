---
title: 列 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの列について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 17406eb94f8e29f4b8ab87653ab41df737fa15ef
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509488"
---
# <a name="columns"></a>[列]

Kusto のすべての[テーブル](tables.md)とすべての表形式のデータ ストリームは、列と行の四角形のグリッドです。 テーブル内のすべての列には、名前と特定の[スカラー データ型](../scalar-data-types/index.md)があります。 テーブルまたは表形式のデータ ストリームの列は順序付けされるため、列はテーブルの列コレクション内の特定の位置も持ちます。

**メモ**  

* 列名では大文字と小文字が区別されます。
* 列名は エンティティ名 の規則に従[います](./entity-names.md)。
* テーブルあたりの列の最大数は 10,000 です。

クエリでは、列は通常名前のみで参照されます。 式内でのみ使用でき、式が表示されるクエリ演算子によってテーブルまたは表形式のデータ ストリームが決まるため、列の名前をスコープを追加する必要はありません。 たとえば、次のクエリでは、名前のない表形式のデータ ストリームがあります ( 単一の列を持つ[データテーブル演算子](../datatableoperator.md)を使用して定義`c`されます。 表形式のデータ ストリームは、その列の値の述語によってフィルター処理され、同じ列を持つ新しい名前のない表形式のデータ ストリームが生成されますが、行数は少なくなります。 [次に、as 演算子](../asoperator.md)は表形式のデータ ストリームに名前を付け、その値がクエリの結果として返されます。
特に、コンテナを参照`c`せずに列が名前で参照される方法に注意してください (実際、そのコンテナには名前がありません)。

```kusto
datatable (c:int) [int(-1), 0, 1, 2, 3]
| where c*c >= 2
| as Result
```

> リレーショナル データベースの世界でよく見られるように、行は**レコード**と呼ばれる場合があり、列は**attributes**と呼ばれることもあります。

列の管理の詳細については、「[列の管理](../../management/columns.md)」を参照してください。