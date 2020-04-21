---
title: MS-TDS クライアントと Kusto - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、AZURE データ エクスプローラーで MS-TDS クライアントと Kusto について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 233eec65c14d76b25b76cc85c7ddd190e5171dfe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523496"
---
# <a name="ms-tds-clients-and-kusto"></a>MS-TDS クライアントとクストー

KUsto は、MS-SQL クライアントの TDS 準拠エンドポイントを実装します。 互換性はプロトコル レベルにあるため、Azure Active Directory 認証を使用して SQL Azure データベースに接続できるライブラリやアプリケーションは、オペレーティング システムまたはランタイムに対して直交して Kusto サーバーと連携します。 KUsto サーバー のドメイン名は、SQL Azure サーバーのように使用するだけです。

SQL 言語レベルでは、Kusto は T-SQL のサブセットと SQL サーバーエミュレーションのサブセットを実装します。 SQL Server の T-SQL と Kusto の実装の主な違いについては、[既知の問題](./sqlknownissues.md)を参照してください。

## <a name="net-sql-client"></a>.NET SQL クライアント

KUsto は、SQL クライアントの Azure アクティブ ディレクトリ認証をサポートします。

詳細については[、「.NET SQL クライアント (ユーザー認証)」](./aad.md#net-sql-client-user)および[「.NET SQL クライアント (アプリケーション認証)」を](./aad.md#net-sql-client-application)参照してください。



## <a name="jdbc"></a>JDBC

マイクロソフト JDBC ドライバーを使用して、AAD 認証を使用して Kusto に接続できます。

*mssql-jdbc* JAR と*adal4j* JAR のバージョンの 1 つとそれらのすべての依存を使用するアプリケーションを作成します。 次に例を示します。

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

JDBC ドライバー クラスを使用するためのアプリケーションを作成する`com.microsoft.sqlserver.jdbc.SQLServerDriver`

接続文字列は次のように使用します。

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

AAD 統合認証モードを使用する場合*は、ActiveDirectory パスワードを**統合された状態に*置き換えます。

詳細については[、JDBC (ユーザー認証)](./aad.md#jdbc-user)および[JDBC (アプリケーション認証) を](./aad.md#jdbc-application)参照してください。

## <a name="odbc"></a>ODBC

ODBC をサポートするアプリケーションは、Kusto に接続できます。

ODBC データ ソースを作成します。
1. ODBC データ ソース アドミニストレータを起動します。
2. 新しいデータ ソースを`Add`作成します (ボタン)。
3. `ODBC Driver 17 for SQL Server` を選択します。
3. データ ソースに名前を付け、フィールドに`Server`Kusto クラスター名を指定します(例:`mykusto.kusto.windows.net`
4. 認証`Active Directory Integrated`オプションを選択します。
5. 次のタブでは、データベースを選択できます。
7. 次のタブで、その他すべての設定の既定値をそのまま使用できます。
8. `Finish`ボタンをクリックすると、接続をテストできるデータ ソースの概要ダイアログが開きます。
9. ODBC ソースをアプリケーションで使用できるようになりました。

ODBC アプリケーションが DSN の代わりに接続文字列を受け入れる場合は、次の接続文字列を使用します。
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

一部の ODBC アプリケーションは、NVARCHAR(MAX) タイプhttps://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driverではうまく動作しません (詳細については、を参照してください)。 このようなアプリケーションで使用される一般的な回避策は、n の値を指定して、返されたデータを NVARCHAR(n) にキャストすることです。 Kusto は文字列型が 1 つしかなく、SQL クライアントの場合は NVARCHAR(MAX) としてエンコードされるため、このような回避策は Kusto では機能しません。 Kusto は、このようなアプリケーションに対して異なる回避策を提供します。 接続文字列を介してすべての文字列を NVARCHAR(n) としてエンコードするように Kusto を設定することができます。 接続文字列の言語フィールドを使用して、チューニング オプションを次の形式で指定`language@OptionName1:OptionValue1,OptionName2:OptionValue2`できます。 

たとえば、次の接続文字列は、文字列を NVARCHAR(8000) としてエンコードするように Kusto に指示します。
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

ODBC ドライバーを使用する PowerShell スクリプトの例:

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  

```



## <a name="linqpad"></a>LINQPad

KUsto と共に、SQL サーバーのように Kusto に接続することで、Linq アプリケーションを使用することができます。
LINQPad を使用して、Linq の互換性を調べることができます。 Kusto を参照したり、SQL クエリを実行したりするためにも使用できます。
LINQPad は、Kusto TDS (SQL) エンドポイントを探索するための推奨ツールです。

SQL サーバーに接続する場合と同様に接続します。 LINQPad では、アクティブ ディレクトリ認証がサポートされています。

1. [`Add connection`] をクリックします。
2. [`Build data context automatically`] を選択します。
3. [LINQPad`Default (LINQ to SQL)`ドライバー] を選択します。
4. プロバイダとして を`SQL Azure`選択します。
5. サーバーの場合は、Kusto クラスターの名前を指定します。`mykusto.kusto.windows.net`
6. ログインの場合は`Windows Authentication (Active Directory)`を選択できます。
7. ボタンを`Test`クリックして、接続を確認します。
8. ボタンを`OK`クリックします。 ブラウザ ウィンドウには、データベースを含むツリー ビューが表示されます。
9. データベース、テーブル、および列を参照できます。
10. クエリ ウィンドウでは、SQL クエリを実行できます。 SQL 言語を指定し、データベースへの接続を選択します。
11. クエリ ウィンドウでは、LINQ クエリを実行することもできます。 例えば、ブラウザウィンドウのテーブルを右クリックします。 選択`Count`オプション。 それを実行してみましょう。

## <a name="azure-data-studio-134-and-above"></a>Azure データ スタジオ (1.3.4 以上)

1. 新しい接続。
2. 接続の種類を`Microsoft SQL Server`選択します。
3. Kusto クラスターの名前をサーバー名として指定します( 例:`mykusto.kusto.windows.net`
4. 認証の種類を`Azure Active Directory - Universal with MFA support`選択します。
5. AAD でプロビジョニングされたアカウントを指定します`myname@contoso.com`(最初にアカウントを追加する)。
6. データベースピッカーを使用してデータベースを選択できます。
7. `Connect`ボタンをクリックすると、データベース ダッシュボードが表示されます。
8. 接続を右クリックし、`New Query`クエリ タブを開くか、`New Query`ダッシュボードのタスクをクリックします。

## <a name="power-bi-desktop"></a>Power BI Desktop

SQL Azure データベースに接続する場合と同様に接続します。

1. で`Get Data``More`を選択`Azure`し、`Azure SQL Database`
2. Kusto サーバー名を指定します。`mykusto.kusto.windows.net`
3. 「ダイレクトクエリ」オプションを使用します。
4. 認証`Microsoft account`(ではなく`Windows`) を`sign in`選択し、 をクリックします。
5. ピッカーに使用可能なデータベースが表示されます。 実際の SQL サーバーと同じように続行します。

## <a name="excel"></a>Excel

SQL Azure データベースに接続する場合と同様に接続します。

1. タブ`Data`で、 `Get Data` `From Azure`、`From Azure SQL Database`
2. Kusto サーバー名を指定します。`mykusto.kusto.windows.net`
3. 認証`Microsoft account`(ではなく`Windows`) を`sign in`選択し、 をクリックします。
4. サインインしたら、 を`Connect`クリックします。
5. ピッカーに使用可能なデータベースが表示されます。 実際の SQL サーバーと同じように続行します。

## <a name="tableau"></a>Tableau

1. ODBC データ ソースを作成する方法については[、「ODBC」](./clients.md#odbc)セクションを参照してください。
2. を介`Other Databases (ODBC)`して接続します。
3. で [ ODBC`DSN`データ ソース ] を選択します。
4. ボタン`Connect`を使用して接続を確立します。
5. ボタン`Sign In`が利用可能になったら、Kustoにログインします。

## <a name="dbeaver-533-and-above"></a>Dビーバー (5.3.3 以上)

Kusto と互換性のある方法で結果セットを処理するように DBeaver を設定します。

1. メニュー`Window`で`Preferences`を選択します。
2. セクション`Editors`で`Data Editor`を選択します。
3. オプション`Refresh data on next page reading`がオンになっていることを確認します。

Kusto データベースへの接続を作成します。

1. 新しいデータベース接続 (`Database`メニューと`New Connection`オプション) を作成します。
2. を探`Azure`して選択`Azure SQL Database`します。 [`Next`] をクリックします。
3. ホスト`mykusto.kusto.windows.net`、例えばデータベースを指定します。 `mydatabase` master をデータベース名のままにしないでください。 Kusto は特定のデータベースへの接続を必要とします。
4. 認証オプションの場合`Active Directory - Password`は、 を選択します。
5. アクティブディレクトリユーザーの資格情報、例えば、このユーザーの`myname@contoso.com`対応するパスワードを指定します。
6. を`Test Connection …`押して、接続の詳細が正しいことを確認します。

## <a name="microsoft-sql-server-management-studio-v18x"></a>SQL サーバー管理スタジオ (v18.x)

1. で`Object Explorer`、 `Connect` `Database Engine`、 .
2. Kusto クラスターの名前をサーバー名として指定します( 例:`mykusto.kusto.windows.net`
3. 認証`Active Directory - Integrated`にオプションを使用します。
4. [`Options`] をクリックします。
5. コンボ`Connect to database`では、オプションを使用して利用可能な`Browse Server`データベースを参照することができます。
6. クリック`Yes`して参照を続行します。
7. ダイアログには、使用可能なすべてのデータベースを含むツリービューが表示されます。 データベースへの接続を続行するには、1 つをクリックします。
8. または、コンボで`Connect to database`を選択`default`することができます。 [`Connect`] をクリックします。 オブジェクト エクスプローラーにすべてのデータベースが表示されます。
9. SSMS では、データベース スキーマを参照するためにサブクエリを相関させるため、SSMS を使用したデータベース オブジェクトの参照はまだサポートされていません。 相関サブクエリは Kusto ではサポートされていません。 [correlated sub-queries](./sqlknownissues.md#correlated-sub-queries)
10. データベースをクリックします。 クエリ`New Query`ウィンドウを開くには、オプションをクリックします。
11. クエリ ウィンドウからカスタム SQL クエリを実行できます。

## <a name="matlab-via-jdbc"></a>マトラボ (JDBC 経由)

必要な JAR ファイルを MATLAB の静的クラスパスの先頭に追加します。 これを行うには、プリファレンスディレクトリに *「javaclasspath.txt」* ファイルを作成します。 Matlab のコマンド ウィンドウで次のコマンドを実行します。 

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

必要な JAR ファイルへの完全パスを追加します。 

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

> このファイルをクラスパスの先頭に追加するには、>前*に<が*必要です。 さらに、"c:\full\path\to"をこれらのファイルへの実際のフルパスに置き換えます。 

MATLAB を再起動して、これらのクラスが実際にロードされていることを確認します。

接続を試みる前に、次のコマンド (MATLAB コマンド ウィンドウ) を実行します。

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> これにより *、TransformerFactory*がデフォルトにリセットされます (MATLAB は通常、これをザクセンでオーバーロードしますが、これは ADAL4J と互換性がありません)。

Kusto TDS エンドポイントに接続するには、次のコマンド (MATLAB コマンド ウィンドウ) を送信します。

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> ここで、これは単に *"database="* で終わり、名前が付かないというのは正しく、"データベース" 関数は自動的に最初の入力 (データベース名) をこの文字列に追加します。
> AAD 統合認証モードを使用する場合*は、ActiveDirectory パスワードを**統合された状態に*置き換えます。

接続をテストし、サンプル クエリを実行します。 次のコマンド (MATLAB コマンド ウィンドウ) を送信します。

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> *KUSTO_TABLEを*Kusto の既存のテーブルに置き換える



## <a name="sending-t-sql-queries-over-the-rest-api"></a>REST API を介した T-SQL クエリの送信

[Kusto REST API](../rest/index.md)は、T-SQL クエリを受け入れて実行できます。
これを`csl`行うには、プロパティを T-SQL クエリ自体のテキストに設定し[、request プロパティ](../netfx/request-properties.md)`OptionQueryLanguage`を値`sql`に設定して、クエリ エンドポイントに要求を送信します。