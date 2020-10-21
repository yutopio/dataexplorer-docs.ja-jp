---
title: 表形式式ステートメント-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのテーブル式ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 943281c2e06aecfffab72ca0f98597c19938d936
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250762"
---
# <a name="tabular-expression-statements"></a>表形式の式ステートメント

テーブル式ステートメントは、通常、クエリについて話すときに注意する必要があるユーザーです。 このステートメントは通常、ステートメントの一覧の最後にあり、入力と出力の両方がテーブルまたは表形式のデータセットで構成されています。

Kusto は、テーブル式ステートメントにデータフローモデルを使用します。 表形式の式ステートメントの一般的な構造は、 *表形式のデータソース* (Kusto テーブルなど)、 *表形式のデータ演算子* (フィルターや予測など)、および *表示*される可能性のある演算子の組み合わせです。 このコンポジションは、パイプ文字 () で表されます。このステートメントには、 `|` 左から右への表形式データのフローを視覚的に表す、非常に標準的な形式が与えられています。
各演算子は、"パイプから" の表形式のデータセットと、演算子の本体からの追加入力 (他の表形式のデータ セットを含む) を受け取り、次に続く演算子に表形式のデータ セットを出力します ()。   

*source1* `|`*operator1* `|`*operator2* `|`*Renderinstruction*

次の例では、ソースは `Logs` (現在のデータベース内のテーブルへの参照) であり、最初の演算子は `where` (レコードごとの述語に従って入力からレコードを除外する)、2番目の演算子は `count` (入力データセット内のレコード数をカウントします) です。

```kusto
Logs | where Timestamp > ago(1d) | count
```

次のより複雑な例では、演算子を使用して `join` 2 つの入力データセットのレコードを結合します。1つはテーブルに対するフィルターで、もう1つは `Logs` テーブルに対するフィルターです。 `Events`

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>表形式のデータソース

**表形式のデータソース**は、**表形式のデータ演算子**によってさらに処理されるレコードのセットを生成します。 Kusto では、次のような多くのソースがサポートされています。

* テーブル参照 (コンテキストデータベース、またはその他のクラスター/データベース内の Kusto テーブルを参照)。
* 表形式の [範囲演算子](rangeoperator.md)。
* [Print 演算子](printoperator.md)。
* テーブルを返す関数の呼び出し。
* [テーブルリテラル](datatableoperator.md)("datatable")。