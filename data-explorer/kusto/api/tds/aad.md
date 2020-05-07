---
title: Azure Active Directory を使用した MS-CHAP-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Azure Active Directory を使用した MS TDS について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 01/02/2019
ms.openlocfilehash: 5511155eaa131c85a49a2082322ad95fcd022418
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862014"
---
# <a name="ms-tds-with-azure-active-directory"></a>Azure Active Directory を使用した MS-CHAP

## <a name="aad-user-authentication"></a>AAD ユーザー認証

AAD ユーザー認証をサポートする SQL クライアントは、Kusto と共に使用できます。

### <a name="net-sql-client-user"></a>.NET SQL クライアント (ユーザー)

たとえば、統合 AAD の場合は次のようになります。
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        Authentication = SqlAuthenticationMethod.ActiveDirectoryIntegrated,
        DataSource = "mykusto.kusto.windows.net"
    };
```

Kusto は、既に取得したアクセストークンを使用した認証をサポートしています。
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        DataSource = "mykusto.kusto.windows.net"
    };
    using (var connection = new SqlConnection(csb.ToString()))
    {
        connection.AccessToken = accessToken;
        await connection.OpenAsync();
    }
```

### <a name="jdbc-user"></a>JDBC (ユーザー)

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.Statement;
import com.microsoft.sqlserver.jdbc.SQLServerDataSource;
import com.microsoft.aad.adal4j.*;

public class Sample {
  public static void main(String[] args) throws Exception {
    AuthenticationResult authenticationResult = futureAuthenticationResult.get();
    SQLServerDataSource ds = new SQLServerDataSource();
    ds.setServerName("<your cluster DNS name>");
    ds.setDatabaseName("<your database name>");
    ds.setHostNameInCertificate("*.kusto.windows.net"); // Or appropriate regional domain.
    ds.setAuthentication("ActiveDirectoryIntegrated");
    try (Connection connection = ds.getConnection();
         Statement stmt = connection.createStatement();) {
      ResultSet rs = stmt.executeQuery("<your T-SQL query>");
      /*
      Read query result.
      */
    } catch (Exception e) {
      System.out.println();
      e.printStackTrace();
    }
  }
}
```

## <a name="aad-application-authentication"></a>AAD アプリケーション認証

Kusto にプロビジョニングされた AAD アプリケーションでは、AAD をサポートする SQL クライアントライブラリを使用して Kusto に接続できます。 AAD アプリケーションの詳細については[、「Aad アプリケーションの作成](../../management/access-control/how-to-provision-aad-app.md)」を参照してください。

### <a name="net-sql-client-application"></a>.NET SQL クライアント (アプリケーション)

*Applicationclientid*と*applicationclientid*を使用して aad アプリケーションをプロビジョニングし、クラスター *ClusterDnsName*上のデータベース*DatabaseName*にアクセスする権限を付与した場合、次のサンプルでは、この Aad アプリケーションからのクエリに .net SQL クライアントを使用する方法を示します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;
using System.Data;
using System.Data.SqlClient;

namespace Sample
{
  class Program
  {
    private static async Task<string> ObtainToken()
    {
      var authContext = new AuthenticationContext(
        // Can also use tenant ID.
        "https://login.microsoftonline.com/<your AAD tenant name>");
      var applicationCredentials = new ClientCredential(
        "<your application client ID>",
        "<your application key>");
      var result = await authContext.AcquireTokenAsync(
        "https://<your cluster DNS name>",
        applicationCredentials);
      return result.AccessToken;
    }

    private static async Task QuerySample()
    {
      var csb = new SqlConnectionStringBuilder()
      {
        InitialCatalog = "<your database name>",
        DataSource = "<your cluster DNS name>"
      };
      using (var connection = new SqlConnection(csb.ToString()))
      {
        connection.AccessToken = await ObtainToken();
        await connection.OpenAsync();
        using (var command = new SqlCommand(
          "<your T-SQL query>",
          connection))
        {
          var reader = await command.ExecuteReaderAsync();
          /*
          Read query result.
          */
        }
      }
    }
  }
}
```

### <a name="jdbc-application"></a>JDBC (アプリケーション)

```java
import java.sql.*;
import com.microsoft.sqlserver.jdbc.*;
import com.microsoft.aad.adal4j.*;

public class Sample {
  public static void main(String[] args) throws Throwable {
    ExecutorService service = Executors.newFixedThreadPool(1);
    // Can also use tenant name.
    String url = "https://login.microsoftonline.com/<your AAD tenant ID>";
    AuthenticationContext authenticationContext =
      new AuthenticationContext(url, false, service);
    ClientCredential  clientCredential = new ClientCredential(
      "<your application client ID>",
      "<your application key>");
    Future<AuthenticationResult> futureAuthenticationResult =
      authenticationContext.acquireToken(
        "https://<your cluster DNS name>",
        clientCredential,
        null);
    AuthenticationResult authenticationResult = futureAuthenticationResult.get();
    SQLServerDataSource ds = new SQLServerDataSource();
    ds.setServerName("<your cluster DNS name>");
    ds.setDatabaseName("<your database name>");
    ds.setAccessToken(authenticationResult.getAccessToken());
    connection = ds.getConnection();
    statement = connection.createStatement();
    ResultSet rs = statement.executeQuery("<your T-SQL query>");
    /*
    Read query result.
    */
  }
}
```