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
ms.openlocfilehash: 55864dd408f35c59398ea1b93f18c0834a611a90
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258098"
---
# <a name="kql-over-tds"></a>TDS 経由の KQL

Kusto を使用すると、TDS エンドポイントでネイティブ[Kql](../../query/index.md)クエリ言語で作成されたクエリを実行できます。 この機能により、Kusto への移行がスムーズになります。 たとえば、KQL クエリを使用して Kusto を照会する SSIS ジョブを作成できます。

## <a name="executing-kusto-stored-functions"></a>Kusto に格納された関数の実行

Kusto は、SQL ストアドプロシージャの呼び出しなど、[ストアド関数](../../query/schema-entities/stored-functions.md)の実行を許可します。

たとえば、格納されている関数 MyFunction は次のようになります。

|名前 |パラメーター|本文|フォルダー|DocString
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

> [!注:] は、という名前の明示的なスキーマで格納されている関数を呼び出し `kusto` て、Kusto に格納されている関数と、SQL システムのエミュレートされたストアドプロシージャを区別します。

また、SQL 表形式関数のように、T-sql から格納されている関数に Kusto 呼び出すこともできます。

```sql
SELECT * FROM kusto.MyFunction(10)
```

最適化された KQL クエリを作成し、格納されている関数にカプセル化することで、T-sql クエリコードを最小限にします。

## <a name="executing-kql-query"></a>KQL クエリの実行

ストアドプロシージャは `sp_execute_kql` 、 [kql](../../query/index.md)クエリ (パラメーター化されたクエリを含む) を実行します。 この手順は、SQL server に似てい `sp_executesql` ます。

の最初のパラメーター `sp_execute_kql` は KQL クエリです。 追加のパラメーターを導入して、[クエリパラメーター](../../query/queryparametersstatement.md)のように動作させることができます。

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

> [!NOTE]
> パラメーターの型はプロトコルによって設定されるため、TDS を使用してを呼び出すときにパラメーターを宣言する必要はありません。
