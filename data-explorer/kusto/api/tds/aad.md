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
ms.openlocfilehash: 6766a817a1568eeb3cbcf2eb5bc0440cdd10eec6
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428431"
---
# <a name="ms-tds-with-azure-active-directory"></a>Azure Active Directory を使用した MS-CHAP

## <a name="azure-ad-user-authentication"></a>ユーザー認証の Azure AD

Azure AD ユーザー認証をサポートする SQL クライアントは、Azure データエクスプローラーで使用できます。

### <a name="net-sql-client-user"></a>.NET SQL クライアント (ユーザー)

たとえば、統合 Azure AD の場合は次のようになります。
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

## <a name="azure-ad-application-authentication"></a>アプリケーション認証の Azure AD

Kusto にプロビジョニングされた Azure AD アプリケーションは、Kusto に接続するための Azure AD をサポートする SQL クライアントライブラリを使用できます。 Azure AD アプリケーションの詳細については、「 [Azure AD アプリケーションの作成](../../management/access-control/how-to-provision-aad-app.md)」を参照してください。

### <a name="net-sql-client-application"></a>.NET SQL クライアント (アプリケーション)

*Applicationclientid*と*applicationclientid*を使用して Azure AD アプリケーションをプロビジョニングし、クラスター *ClusterDnsName*上のデータベース*DatabaseName*にアクセスするためのアクセス許可が付与されていることを前提として、次のサンプルでは、この Azure AD アプリケーションからのクエリに .net SQL クライアントを使用する方法を示します。

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