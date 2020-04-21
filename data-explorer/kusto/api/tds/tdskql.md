---
title: TDS 経由の KQL - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの TDS に対する KQL について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 364db99141c528f4a822692124ebb870d7315bca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523309"
---
# <a name="kql-over-tds"></a>TDS 経由の KQL

Kusto は、ネイティブ[KQL](../../query/index.md)クエリ言語で作成されたクエリを実行するために TDS エンドポイントを利用することができます。 この機能は、Kustoに向かってより滑らかなミジレーションを提供します。 たとえば、KQL クエリを使用して Kusto をクエリする SSIS ジョブを作成できます。

## <a name="executing-kusto-stored-functions"></a>Kusto ストアドファンクションの実行

Kusto では、SQL ストアド プロシージャを呼び出す場合と同様に、[ストアドファンクション](../../query/schema-entities/stored-functions.md)を実行できます。

たとえば、ストアドファンクション MyFunction:

|名前 |パラメーター|Body|Folder|ドクスト文字列
|---|---|---|---|---
|マイファンクション |(マイリミット:ロング)| {ストームイベント&#124;私の制限を制限します}|マイフォルダ|パラメーター付きのデモ関数||

次のように呼び出すことができます。

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("kusto.MyFunction", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

Kusto ストアドファンクションとエミュレートされた SQL`kusto`システムストアドプロシージャを区別するために、 という名前の明示的なスキーマを使用してストアド関数を呼び出す必要があります。

KUsto ストアドファンクションは、SQL 表関数と同様に、T-SQL からも呼び出すことができます。

```sql
SELECT * FROM kusto.MyFunction(10)
```

最適化された KQL クエリを作成し、それらを格納された関数にカプセル化して、T-SQL クエリ コードを最小限にすることをお勧めします。

## <a name="executing-kql-query"></a>KQL クエリの実行

SQL Server`sp_executesql`と同様に、Kusto`sp_execute_kql`は、パラメータ化クエリを含む[KQL](../../query/index.md)クエリを実行するためのストアド プロシージャを導入しました。

の第 1`sp_execute_kql`パラメータは KQL クエリです。 追加のパラメータを導入することができ、クエリ[パラメータ](../../query/queryparametersstatement.md)のように動作します。

次に例を示します。

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("sp_execute_kql", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var query = new SqlParameter("@kql_query", SqlDbType.NVarChar);
      command.Parameters.Add(query);
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      query.Value = "StormEvents | limit myLimit";
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

パラメータ型はプロトコルを介して設定されるため、TDSを介して呼び出す際にパラメータを宣言する必要はありません。