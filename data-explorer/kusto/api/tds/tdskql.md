---
title: KQL over TDS-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの TDS 経由の KQL について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 2c4443c0a9301dbc6bb3e65392163da0cc237f74
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617886"
---
# <a name="kql-over-tds"></a>TDS 経由の KQL

Kusto を使用すると、TDS エンドポイントでネイティブ[Kql](../../query/index.md)クエリ言語で作成されたクエリを実行できます。 この機能により、Kusto への移行がスムーズになります。 たとえば、KQL クエリを使用して Kusto を照会する SSIS ジョブを作成できます。

## <a name="executing-kusto-stored-functions"></a>Kusto に格納された関数の実行

Kusto は、SQL ストアドプロシージャの呼び出しなど、[ストアド関数](../../query/schema-entities/stored-functions.md)の実行を許可します。

たとえば、格納されている関数 MyFunction は次のようになります。

|名前 |パラメーター|Body|Folder|DocString
|---|---|---|---|---
|MyFunction |(myLimit: long)| {StormEvents &#124; 制限 myLimit}|MyFolder|パラメーターを使用したデモ関数||

次のようにを呼び出すことができます。

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

> [!注:] は、という名前`kusto`の明示的なスキーマで格納されている関数を呼び出して、Kusto に格納されている関数と、SQL システムのエミュレートされたストアドプロシージャを区別します。

また、SQL 表形式関数のように、T-sql から格納されている関数に Kusto 呼び出すこともできます。

```sql
SELECT * FROM kusto.MyFunction(10)
```

最適化された KQL クエリを作成し、格納されている関数にカプセル化することで、T-sql クエリコードを最小限にします。

## <a name="executing-kql-query"></a>KQL クエリの実行

ストアドプロシージャ`sp_execute_kql`は、 [kql](../../query/index.md)クエリ (パラメーター化されたクエリを含む) を実行します。 この手順は、SQL server `sp_executesql`に似ています。

の最初の`sp_execute_kql`パラメーターは kql クエリです。 追加のパラメーターを導入して、[クエリパラメーター](../../query/queryparametersstatement.md)のように動作させることができます。

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

> [!注:] パラメーターの型はプロトコルによって設定されるため、TDS を使用してを呼び出すときにパラメーターを宣言する必要はありません。
