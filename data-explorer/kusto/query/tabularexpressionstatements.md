---
title: 表形式の式ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの表形式の式ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1209f96ed99de79d3fa6ac00e3a115a00a6248e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506615"
---
# <a name="tabular-expression-statements"></a>表形式の式ステートメント

表形式の式ステートメントは、クエリについて話すときに、通常は注意を念頭に置くものです。 このステートメントは通常、ステートメント・リストの最後に現れ、入力と出力の両方が表または表形式のデータ・セットで構成されます。

Kusto は、表形式の式ステートメントにデータ フロー モデルを使用します。 表形式の式ステートメントの一般的な構造は *、表形式のデータ ソース*(Kusto テーブルなど)、*表形式データ演算子*(フィルターや射影など)、レンダリング*演算子*の組成です。 コンポジションはパイプ文字 (`|`) によって表され、表形式のデータの流れを視覚的に左から右に表現する非常に規則的な形式をステートメントに与えます。
各演算子は、"パイプから" 表形式のデータ セットを受け取り、その演算子本体からの追加入力 (他の表形式のデータセットを含む) を受け取り、次の演算子に対して表形式のデータセットを出力します。   

*ソース1*`|`*オペレーター1*`|`*オペレーター2*`|`*レンダリング命令*

次の例では、ソースは`Logs`(現在のデータベース内のテーブルへの参照) 、最初の演算子`where`は (レコードごとの述語に従って入力からレコードをフィルター処理する) 、2`count`番目の演算子は (入力データ セット内のレコード数をカウントします) です。

```kusto
Logs | where Timestamp > ago(1d) | count
```

次のより複雑な例では、`join`演算子を使用して、`Logs`テーブルのフィルターとテーブルのフィルターの 2 つの入力データ セットからのレコードを`Events`結合します。

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>表形式のデータ ソース

**表形式のデータ ソース**は、**表形式データ演算子**によってさらに処理されるレコードのセットを生成します。 Kusto は、次のような多数のソースをサポートしています。

* テーブル参照 (コンテキスト データベースまたはその他のクラスタ/データベース内の Kusto テーブルを参照します)。
* 表形式の[範囲演算子](rangeoperator.md)。
* [印刷演算子](printoperator.md)。
* テーブルを返す関数の呼び出し。
* [テーブルリテラル](datatableoperator.md)("データテーブル")。