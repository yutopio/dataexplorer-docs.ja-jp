---
title: MS TDS クライアントと Kusto-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの MS TDS クライアントと Kusto について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/30/2019
ms.openlocfilehash: b41f77fe97ce6adeeade63c00824818f4a3af721
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862039"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>MS TDS クライアントと Azure データエクスプローラー

Azure データエクスプローラーは、MS SQL クライアントの TDS 準拠のエンドポイントを実装します。 プロトコルレベルで互換性があります。 Azure Active Directory (Azure AD) 認証を使用して SQL Azure データベースに接続できるライブラリまたはアプリケーションは、Azure データエクスプローラーサーバーで動作します。 そのため、SQL Azure サーバーのように、サーバーのドメイン名を使用できます。

Azure データエクスプローラーは、T-sql のサブセットと SQL server エミュレーションのサブセットを実装します。 詳細については、SQL Server の T-sql と Azure データエクスプローラーの実装の違いに関する[既知の問題](./sqlknownissues.md)を参照してください。

## <a name="net-sql-client"></a>.NET SQL クライアント

Azure データエクスプローラーでは、SQL クライアントの Azure AD 認証がサポートされています。 詳細については、「 [.NET Sql クライアント (ユーザー認証)](./aad.md#net-sql-client-user) 」および「 [.net sql クライアント (アプリケーション認証)](./aad.md#net-sql-client-application) 」を参照してください。

## <a name="jdbc"></a>JDBC

Microsoft JDBC driver は、Azure AD 認証を使用して Azure データエクスプローラーに接続するために使用できます。

*Mssql-jdbc* jar と*adal4j* jar のいずれかのバージョンと、そのすべての依存関係を使用するアプリケーションを作成します。
たとえば、次のように入力します。

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

JDBC driver クラス*SQLServerDriver*を使用するためのアプリケーションを作成します。

次のような接続文字列を使用します。

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> Azure Active Directory 統合認証モードを使用する場合は、 *ActiveDirectoryPassword*を*ActiveDirectoryIntegrated*に置き換えます。 詳細については、「 [jdbc (ユーザー認証)](./aad.md#jdbc-user) 」と「 [jdbc (アプリケーション認証)](./aad.md#jdbc-application)」を参照してください。

## <a name="odbc"></a>ODBC

ODBC をサポートするアプリケーションは、Azure データエクスプローラーに接続できます。

ODBC データソースを作成します。

1. ODBC データソースアドミニストレーターを起動します。
2. [**追加**] を選択して新しいデータソースを作成し、 *ODBC Driver 17 を SQL Server に*設定します。
3. データソースに名前を付け、[**サーバー** ] フィールドに Azure データエクスプローラークラスター名を指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
4. 認証オプションとして [ **Active Directory 統合**] を設定します。
5. [**次へ**] を選択して、データベースを設定します。
7. 次のタブでは、他のすべての設定の既定値をそのままにしておくことができます。
8. [**完了**] を選択して、[データソースの概要] ウィンドウを開きます。このウィンドウで接続をテストできます。

これで、アプリケーションで ODBC データソースを使用できるようになりました。

ODBC アプリケーションが、ではなく、または DSN に加えて接続文字列を受け入れることができる場合は、次を使用します。

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

一部の ODBC アプリケーションは、型で`NVARCHAR(MAX)`は適切に機能しません。 詳細については、「https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver」を参照してください。

一般的な回避策は、返されたデータを*NVARCHAR (n)* にキャストすることです。 n の値はいくつかあります。 たとえば、 *NVARCHAR (4000)* のようになります。 ただし、このような回避策は、azure データエクスプローラーでは機能しません。これは、Azure データエクスプローラーの文字列型が1つだけであり、SQL クライアント用に*NVARCHAR (MAX)* としてエンコードされているためです。

Azure データエクスプローラーでは、別の回避策が提供されます。 接続文字列を使用してすべての文字列を*NVARCHAR (n)* としてエンコードするように Azure データエクスプローラーを構成できます。 接続文字列の language フィールドは、、 * language@OptionName1:OptionValue1OptionName2: OptionValue2*の形式でチューニングオプションを指定するために使用できます。

たとえば、次の接続文字列は、文字列を*NVARCHAR (8000)* としてエンコードするように Azure データエクスプローラーに指示します。

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

ODBC ドライバーを使用する PowerShell スクリプトの例を次に示します。

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()
```

## <a name="linqpad"></a>LINQPad

Linq アプリケーションは、SQL server と同じように接続することによって、Azure データエクスプローラーと共に使用できます。
LINQPad を使用して、Linq の互換性を調べ、Azure データエクスプローラーを参照します。 また、SQL クエリを実行することもできます。また、Azure データエクスプローラー TDS (SQL) エンドポイントを調べるために推奨されるツールです。

ユーザーのように、Microsoft SQL Server に接続します。 LINQPad は Active Directory 認証をサポートしています。

1. **[接続の追加]** を選択します。
2. **ビルドデータコンテキストを自動的に**設定します。
3. LINQPad ドライバーの**既定値 (LINQ to SQL)** を設定します。
4. **SQL Azure**を設定します。
5. サーバーでは、Azure データエクスプローラークラスターの名前を指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
6. サインインに**Windows 認証 (Active Directory)** を設定します。
7. [**テスト**] を選択して接続を確認します。
8. **[OK]** を選択します。 ブラウザーウィンドウに、データベースと共にツリービューが表示されます。
9. データベース、テーブル、および列を参照できます。
10. クエリウィンドウで SQL クエリを実行できます。 SQL 言語を指定し、データベースへの接続を選択します。
11. クエリウィンドウで LINQ クエリを実行することもできます。 たとえば、ブラウザーウィンドウでテーブルを選択します。 [**カウント**] を選択し、実行を許可します。

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 以降)

新しい接続を作成します。

1. 接続の種類を**Microsoft SQL Server**に設定します。
2. Azure データエクスプローラークラスターの名前をサーバー名として指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
3. 認証の種類**Azure Active Directory-MFA を使用したユニバーサル**認証を設定します。
4. Azure AD にプロビジョニングされているアカウントを指定します。 たとえば、 *myname@contoso.com* です。 初めてアカウントを追加します。
5. データベースピッカーを使用してデータベースを選択します。
6. [**接続**] を選択してデータベースダッシュボードに移動し、接続を設定します。
7. [**新しいクエリ**] を選択してクエリウィンドウを開くか、ダッシュボードで [**新しいクエリ**] タスクを選択します。

## <a name="power-bi-desktop"></a>Power BI Desktop

ユーザーのように、SQL Azure データベースに接続します。

1. [**データの取得**] で [**詳細**] を選択します。
2. **Azure**を設定し、 **Azure SQL Database**します。
3. Azure データエクスプローラーサーバー名を指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
4. [ **DirectQuery**] を選択します。
5. ( **Windows**ではなく) **Microsoft アカウント**認証を設定し、[**サインイン**] を選択します。
6. データベースピッカーには、使用可能なデータベースが表示されます。 実際の SQL server の場合と同様に、続行します。

## <a name="excel"></a>Excel

ユーザーのように、SQL Azure データベースに接続します。

1. [**データ**] タブの [**データの取得**] を選択し、[ **Azure**および**Azure SQL Database から**設定します。
2. Azure データエクスプローラーサーバー名を指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
3. ( **Windows**ではなく) **Microsoft アカウント**認証を設定し、[**サインイン**] を選択します。
5. データベースピッカーには、使用可能なデータベースが表示されます。 実際の SQL server の場合と同様に、続行します。
4. サインインしたら、[**接続**] を選択します。
5. データベースピッカーには、使用可能なデータベースが表示されます。 実際の SQL server の場合と同様に、続行します。

## <a name="tableau"></a>Tableau

ODBC データソースを作成します。 詳細については、「 [ODBC](./clients.md#odbc) 」セクションを参照してください。

1. 他のデータベースを使用した接続 **(ODBC)**。
2. **DSN**で ODBC データソースを設定します。
3. [**接続] を選択**して接続を確立します。
4. ボタンが使用可能になったら [**サインイン**] を選択し、Azure データエクスプローラーにサインインします。

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 以上)

Azure データエクスプローラーと互換性のある方法で結果セットを処理するように DBeaver を構成します。

1. [**ウィンドウ**] メニューの [**設定**] を選択します。
2. [**エディター** ] セクションの [**データエディター** ] を選択します。
3. **次のページの読み取り時にデータを更新**するように設定されていることを確認します。

Azure データエクスプローラーデータベースへの接続を作成します。

1. [**データベース**] メニューの [**新しい接続**] をクリックします。
2. **Azure**を検索し、 **Azure SQL Database**を設定します。 **[次へ]** を選択します。
3. ホストを指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
4. データベースを指定します。 たとえば、 *mydatabase*のようになります。

> [!WARNING]
> *Master*をデータベース名として使用しないでください。 Azure データエクスプローラーには、特定のデータベースへの接続が必要です。

5. **Active Directory-** *Authentication*のパスワードを設定します。
6. Active directory ユーザーの資格情報を指定します。 たとえば、 *myname@contoso.com*「」と入力し、このユーザーに対応するパスワードを設定します。
7. **テスト接続の**選択... 接続の詳細が正しいことを確認します。

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio (v18)

1. [**接続**] を選択し、[**オブジェクトエクスプローラー**] の下の**データベースエンジン**します。
2. サーバー名として Azure データエクスプローラークラスターの名前を指定します。 たとえば、 *mykusto.kusto.windows.net*のようになります。
3. 認証用に**統合 Active Directory**を設定します。
4. [**オプション**] を選択します。
5. [**データベースへの接続**] で [**サーバーの参照**] を選択して、使用可能なデータベースを参照します。
6. [**はい**] を選択してブラウズを続行します。
7. ウィンドウには、使用可能なすべてのデータベースを含むツリービューが表示されます。 データベースに接続するには、1つを選択します。
8. 別の方法として、[**データベースへの接続**] で [**既定**] を選択し、[**接続**] を選択します。 オブジェクトエクスプローラーにすべてのデータベースが表示されます。

> [!NOTE]
> Ssms ではサブクエリを使用してデータベーススキーマを参照するため、SSMS を使用したデータベースオブジェクトの参照はまだサポートされていません。
> 相関サブクエリは、Azure データエクスプローラーではサポートされていません。 詳細については、「[相関サブクエリ](./sqlknownissues.md#correlated-sub-queries)」を参照してください。

10. [**新しいクエリ**] を選択してクエリウィンドウを開き、データベースを設定します。

クエリウィンドウからカスタム SQL クエリを実行できます。

## <a name="matlab-via-jdbc"></a>MATLAB (JDBC 経由)

基本設定のディレクトリに *"javaclasspath"* ファイルを作成して、必要な JAR ファイルを MATLAB の静的クラスパスの前に追加します。

1. Matlab のコマンドウィンドウで、次のコマンドを実行します。

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. 必要な JAR ファイルへの完全なパスを追加します。

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> [!NOTE]
> これらのファイルはクラスパスの先頭に追加されるように、先頭に>**する前**に <が必要です。 また、 *c:\full\path\to*をこれらのファイルの実際の完全パスに置き換えます。

3. MATLAB を再起動して、これらのクラスが読み込まれるようにします。

4. 接続を試行する前に、次のコマンドを実行します (MATLAB コマンドウィンドウ)。

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> これにより、 **Transformerfactory**が既定値にリセットされます (通常、MATLAB は**ザクセン**でこれをオーバーロードしますが、これは**ADAL4J**と互換性がありません)。

5. 次のコマンドを使用して、Azure データエクスプローラー TDS エンドポイントに接続します (MATLAB コマンドウィンドウ)。

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> **"Database ="** で終了し、値を指定しなくても問題はありません。 *データベース*関数は、最初の入力 (データベース名) をこの文字列に自動的に追加します。
> Azure Active Directory 統合認証モードを使用する場合は、 *ActiveDirectoryPassword*を*ActiveDirectoryIntegrated*に置き換えます。

6. 接続をテストし、サンプルクエリを実行します。 次のコマンドを送信します (MATLAB コマンドウィンドウ)。

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> *KUSTO_TABLE*を Azure データエクスプローラーの既存のテーブルに置き換えます。

## <a name="sending-t-sql-queries-over-the-rest-api"></a>REST API に対して T-sql クエリを送信する

[Azure データエクスプローラー REST API](../rest/index.md)は、t-sql クエリを受け入れて実行できます。

1. 要求をクエリエンドポイントに送信します。これには、 **csl**プロパティが t-sql クエリのテキストに設定されています。
2. **[要求プロパティ](../netfx/request-properties.md)** **OptionQueryLanguage**を**sql**に設定します。
