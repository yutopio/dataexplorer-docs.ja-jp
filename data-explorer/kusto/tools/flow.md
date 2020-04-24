---
title: マイクロソフト フロー Azure クストー コネクタ (プレビュー) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Microsoft フロー Azure クストー コネクタ (プレビュー) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: c4e732afb9e6165f803b9dc7e801b4e2be4c2588
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504099"
---
# <a name="microsoft-flow-azure-kusto-connector-preview"></a>マイクロソフト フロー Azure クストー コネクタ (プレビュー)

Microsoft Flow Azure Kusto コネクタを使用すると、スケジュールされたタスクまたはトリガーされたタスクの一部として、自動的に[Kusto](https://flow.microsoft.com/)クエリとコマンドを実行できます。

一般的な使用シナリオには、次のものがあります。

* テーブルとグラフを含めた日次レポートの送信
* クエリ結果に基づいて通知を設定する
* クラスターでの制御コマンドのスケジュール設定
* Azure Data Explorer と他のデータベース間のデータのエクスポートとインポート 

## <a name="limitations"></a>制限事項

1. クライアントに返される結果は、500,000 レコードに制限されています。 これらのレコードのメモリ全体は、64 MB および 7 分の実行時間を超えることはできません。
2. 現在、コネクタは[フォーク](../query/forkoperator.md)オペレータと[ファセット](../query/facetoperator.md)オペレータをサポートしていません。
3. フローは、インターネットエクスプローラとChromeで最も効果的です。

##  <a name="login"></a>ログイン 

1. [マイクロソフトフロー](https://flow.microsoft.com/)にログインします。

1. Azure Kusto コネクタに初めて接続するときに、サインインを求めるメッセージが表示されます。

1. [**サインイン**] ボタンをクリックし、資格情報を入力して Azure Kusto Flow の使用を開始します。

![alt text](./Images/KustoTools-Flow/flow-signin.png "フローサイン")

## <a name="authentication"></a>認証

Azure Kusto フロー コネクタへの認証は、ユーザー資格情報または AAD アプリケーションを使用して実行できます。



### <a name="aad-application-authentication"></a>AAD アプリケーション認証

AAD アプリケーションを使用して Azure Kusto Flow に認証するには、次の手順を使用します。

> 注: アプリケーションが[AAD アプリケーション](../management/access-control/how-to-provision-aad-app.md)であり、クラスターでの照会の実行が許可されていることを確認してください。

1. Azure Kusto コネクタの右上にある 3 つのドットをクリック![します。](./Images/KustoTools-Flow/flow-addconnection.png "フロー追加接続")

1. [新しい接続の追加] を選択し、[サービス プリンシパルに接続] をクリックします。
![alt text](./Images/KustoTools-Flow/flow-signin.png "フローサイン")

1. アプリケーション ID、アプリケーション キー、およびテナント ID を入力します。

たとえば、マイクロソフトのテナント ID は 72f988bf-86f1-41af-91ab-2d7cd011db47 です。
[接続名] の値は、追加された新しい接続を認識するための文字列です。
![alt text](./Images/KustoTools-Flow/flow-appauth.png "フローアプゴート")

認証が完了すると、フローが追加された新しい接続を使用していることを確認できます。
![alt text](./Images/KustoTools-Flow/flow-appauthcomplete.png "フロー・アプゴート完了")

このフローは、アプリケーションの資格情報を使用して実行されます。

## <a name="find-the-azure-kusto-connector"></a>Azure Kusto コネクタを見つける

Azure Kusto コネクタを使用するには、まずトリガーを追加する必要があります。 トリガーは、繰り返しの期間に基づいて定義することも、以前のフローアクションに対する応答として定義することもできます。

1. [新しいフローを作成します。](https://flow.microsoft.com/manage/flows/new)
2. 最初の手順として[スケジュール - 繰り返し]を追加します。
3. 2 番目の手順の検索ボックスに「Azure Kusto」と入力します。

次の図に示すように、Azure Kusto を表示できます。

![alt text](./Images/KustoTools-Flow/flow-actions.png "フローアクション")

## <a name="azure-kusto-flow-actions"></a>Azure Kusto フロー アクション

フローで Azure Kusto コネクタを検索すると、フローに追加できる 3 つのアクションが表示されます。

次のセクションでは、各 Azure Kusto フロー アクションの機能とパラメーターについて説明します。

![alt text](./Images/KustoTools-Flow/flow-actions.png "フローアクション")

### <a name="azure-kusto---run-query-and-visualize-results"></a>Azure Kusto - クエリの実行と結果の視覚化

> 注: クエリがドットで始まる場合 ([つまり、コントロール コマンド](../management/index.md)です)、[コントロール コマンドを実行して結果を視覚化](./flow.md#azure-kusto---run-control-command-and-visualize-results)してください。

Kusto クエリの結果をテーブルまたはチャートとして視覚化するには、「Azure Kusto - クエリを実行して結果を視覚化する」アクションを使用します。 このアクションの結果は、後で電子メールで送信できます。 たとえば、毎日の ICM レポートを取得する場合に、このフローを使用できます。 

![alt text](./Images/KustoTools-Flow/flow-runquery.png "フローランクエリ")

この例では、クエリの結果が HTML テーブルとして返されます。

### <a name="azure-kusto---run-control-command-and-visualize-results"></a>Azure Kusto - 制御コマンドを実行し、結果を視覚化する

「Azure Kusto - クエリを実行し、結果を視覚化する」アクションと同様に、「Azure Kusto - コントロール[コマンド](../management/index.md)を実行し、結果を視覚化する」アクションを使用してコントロール コマンドを実行することもできます。
このアクションの結果は、後で、テーブルまたはチャートとして電子メールで送信できます。

![alt text](./Images/KustoTools-Flow/flow-runcontrolcommand.png "フローランコントロールコマンド")

この例では、制御コマンドの結果は円グラフとしてレンダリングされます。

### <a name="azure-kusto---run-query-and-list-results"></a>Azure Kusto - クエリの実行と結果の一覧表示

> 注: クエリがドットで始まる場合 ([つまりコントロール コマンド](../management/index.md)です)、コントロール[コマンドの実行と結果の視覚化](./flow.md#azure-kusto---run-control-command-and-visualize-results)を行ってください。

このアクションは Kusto クラスターにクエリを送信します。 後で追加されるアクションは、クエリの結果の各行に対して反復処理されます。

次の例では、1 分ごとにクエリをトリガーし、クエリ結果に基づいて電子メールを送信します。 クエリはデータベース内の行数をチェックし、行数が 0 より大きい場合にのみ電子メールを送信します。 

![alt text](./Images/KustoTools-Flow/flow-runquerylistresults.png "フロー実行クエリリストの結果")

列に複数の線がある場合は、列の各行に対して次のコネクタが実行されます。

## <a name="email-kusto-query-results"></a>電子メール Kusto クエリの結果

電子メール レポートを送信するには、次の手順を実行します。 

![alt text](./Images/KustoTools-Flow/flow-sendemail.png "フロー送信電子メール")

1. 「+ 新規ステップ」をクリックし、「アクションを追加」をクリックします。
2. 検索ボックスに「Office 365 Outlook - 電子メールを送信する」と入力します。
3. メールアドレスに「宛先」を設定し、テキストに「件名」を設定し、動的コンテンツから「本文」フィールドに「本文」を追加します。
4. [詳細オプション] をクリックし、[添付ファイル名] フィールドに [添付ファイル名] を、[添付ファイルのコンテンツ] フィールドに [添付ファイルの内容] を追加し、[HTML] が [はい] に設定されていることを確認します。
5. 上部のバーで、このフローの「フロー名」を設定します。
6. 「フローを作成」をクリックすると完了です!

## <a name="how-to-make-sure-flow-succeeded"></a>フローが成功したことを確認するには?

Microsoft[フローのホームページ](https://flow.microsoft.com/)に移動し、[[マイ フロー](https://flow.microsoft.com/manage/flows) ] をクリックして、[i] ボタンをクリックします。

![alt text](./Images/KustoTools-Flow/flow-myflows.png "フロー-マイフロー")

すべてのフロー実行は、ステータス、開始時間、および期間とともにリストされます。

![alt text](./Images/KustoTools-Flow/flow-runs.png "フローラン")

フローの最後の実行を開くとき、フローのすべてのステップが緑色の V でマークされている場合、フローは正常に終了しました。
それ以外の場合は、赤い感嘆符でマークされたステップを展開して、エラーの詳細を表示します。

![alt text](./Images/KustoTools-Flow/flow-failed.png "フローに失敗しました")

一部のエラーは、クエリ構文エラーなど、自分で簡単に解決できます。

![alt text](./Images/KustoTools-Flow/flow-syntaxerror.png "フロー構文エラー")



## <a name="having-a-timeout-exception"></a>タイムアウト例外が発生していますか?

フローが失敗し、7 分以上実行されると "RequestTimeout" 例外が返される可能性があります。

タイムアウト例外が発生するまでに、7 分のクエリを実行できる最大時間フローであることに注意してください。

マイクロソフト フローの制限事項については、[ここをクリックしてください](#limitations)。

時間が制限されず、変更できる Kusto エクスプローラでも、同じクエリが正常に実行される場合があります。

"RequestTimeout" 例外は、次の図に示されています。

![alt text](./Images/KustoTools-Flow/flow-requesttimeout.png "フロー要求タイムアウト")

問題を解決するには、次の手順を実行します。
1. クエリのベスト プラクティスの詳細については、「[クエリのベスト プラクティス](../query/best-practices.md)」を参照してください。
2. クエリの実行速度を速くするためにクエリをより効率的にするか、チャンクに分割して、各チャンクをクエリの別の部分で実行するようにしてください。

## <a name="usage-examples"></a>使用例

このセクションでは、Azure Kusto フロー コネクタを使用する一般的な例をいくつか紹介します。

### <a name="example-1---azure-kusto-flow-and-sql"></a>例 1 - Azure クストー フローと SQL

Azure Kusto フローを使用してデータをクエリし、それを SQL DB に蓄積できます。 
> 注: SQL 挿入は行ごとに分けて行われていますが、これは少量の出力データに対してのみ使用してください。 

![alt text](./Images/KustoTools-Flow/flow-sqlexample.png "フロー-sqlの例")

### <a name="example-2---push-data-to-power-bi-dataset"></a>例 2 - Power BI データセットへのデータのプッシュ

Azure Kusto フロー コネクタを Power BI コネクタと共に使用して、Kusto クエリから Power BI ストリーミング データセットにデータをプッシュできます。

まず、新しい 'Kusto - クエリを実行して結果を一覧表示' アクションを作成します。

![alt text](./Images/KustoTools-Flow/flow-listresults.png "フローリストの結果")

[新しいステップ] をクリックし、[アクションの追加] を選択して 、[Power BI] を検索します。 [Power BI - データセットへの行の追加] をクリックします。 

![alt text](./Images/KustoTools-Flow/flow-powerbiconnector.png "フローパワービコネクタ")

データのプッシュ先となるワークスペース、データセット、およびテーブルを入力します。
動的コンテンツ ウィンドウからデータセット スキーマと関連する Kusto クエリの結果を含むペイロードを追加します。 

![alt text](./Images/KustoTools-Flow/flow-powerbifields.png "フローパワービフィールズ")

フローは Kusto クエリの結果テーブルの各行に自動的に Power BI アクションを適用することに注意してください。 

![alt text](./Images/KustoTools-Flow/flow-powerbiforeach.png "フローパワービフォーリーチ")

### <a name="example-3---conditional-queries"></a>例 3 - 条件付きクエリ

Kusto クエリの結果は、次のフロー アクションの入力または条件として使用できます。

次の例では、最後の日に発生したインシデントを Kusto に照会します。 解決された各インシデントについて、その問題に関する余裕期間メッセージを投稿し、プッシュ通知を作成します。
まだアクティブな各インシデントについて、同様のインシデントに関する詳細を Kusto に問い合わせ、その情報を電子メールとして送信し、関連する TFS タスクを開きます。

同様のフローを作成するには、次の手順に従います。

新しい 'Kusto - クエリを実行して結果を一覧表示' アクションを作成します。
「新規ステップ」をクリックし、「条件を追加」を選択します。 

![alt text](./Images/KustoTools-Flow/flow-listresults.png "フローリストの結果")

次のアクションの条件として使用するパラメータを、動的コンテンツ ウィンドウから選択します。
指定されたパラメーターに特定の条件を設定するには、リレーションシップの種類と値を選択します。

![alt text](./Images/KustoTools-Flow/flow-condition.png "フロー条件")

この条件は、クエリ結果テーブルの各行に適用されます。

条件が true および false の場合のアクションを追加します。

![alt text](./Images/KustoTools-Flow/flow-conditionactions.png "フロー条件アクション")

Kusto クエリの結果値を動的コンテンツ ウィンドウから選択することで、次のアクションの入力として使用できます。
以下では、"Slack - メッセージの投稿" アクションと 、Kusto クエリのデータを含む [Visual Studio - 新しい作業項目を作成する] アクションを追加しました。

![alt text](./Images/KustoTools-Flow/flow-slack.png "フロースラック")

![alt text](./Images/KustoTools-Flow/flow-visualstudio.png "フロービジュアルスタジオ")

この例では、インシデントがまだアクティブな場合、同じソースからのインシデントが過去にどのように解決されたかに関する情報を取得するために Kusto を再度照会します。

![alt text](./Images/KustoTools-Flow/flow-conditionquery.png "フロー条件クエリ")

この情報を円グラフとして視覚化し、チームに電子メールで送信します。

![alt text](./Images/KustoTools-Flow/flow-conditionemail.png "フローコンディショション電子メール")

### <a name="example-4---email-multiple-azure-kusto-flow-charts"></a>例 4 - 複数の Azure Kusto フロー チャートを電子メールで送信する

「繰り返し」トリガーを使用して新しいフローを作成し、フローの間隔と頻度を定義します。 

1 つ以上の 'Kusto - クエリを実行して結果を視覚化する' アクションを使用して、新しいステップを追加します。 

![alt text](./Images/KustoTools-Flow/flow-severalqueries.png "フロー-複数のクエリ")

各 'Kusto - クエリを実行して結果を視覚化' には、次のフィールドを定義します。

クラスター名、データベース名、クエリ、およびチャートの種類 (HTML テーブル/円グラフ/時間グラフ/棒グラフ/カスタム値を入力します)。

![alt text](./Images/KustoTools-Flow/flow-visualizeresultsmultipleattachments.png "フロー可視化結果複数の添付ファイル")

「Kusto - クエリを実行して結果を視覚化」アクションを終了したら、「メールを送信」アクションを追加します。 

クエリの視覚化結果を電子メールの本文に添付するために、必要な本文を"Body"フィールドに挿入してください。
また、メールに添付ファイルを追加するには、「添付ファイル名」と「添付ファイルのコンテンツ」を追加します。

「はい」フィールドの下で「はい」を選択してください。

![alt text](./Images/KustoTools-Flow/flow-emailmultipleattachments.png "フロー電子メールの複数の添付ファイル")

結果:

![alt text](./Images/KustoTools-Flow/flow-resultsmultipleattachments.png "フロー結果複数添付ファイル")

![alt text](./Images/KustoTools-Flow/flow-resultsmultipleattachments2.png "フロー結果複数の添付ファイル2")

### <a name="example-5---send-a-different-email-to-different-contacts"></a>例 5 - 別の連絡先に別の電子メールを送信する

Azure Kusto Flow を活用して、異なる連絡先に異なるカスタマイズされた電子メールを送信できます。 電子メール アドレスと電子メールの内容は Kusto クエリの結果です。

以下の例を参照してください。

![alt text](./Images/KustoTools-Flow/flow-dynamicemailkusto.png "フローダイナミックエメールクスト")

![alt text](./Images/KustoTools-Flow/flow-dynamicemail.png "フローダイナミックメール")

### <a name="example-6---create-custom-html-table"></a>例 6 - カスタム HTML テーブルの作成

Azure Kusto Flow を利用して、カスタム HTML テーブルなどのカスタム HTML 要素を作成して使用できます。

カスタム HTML テーブルを作成する方法を次の例に示します。 HTML テーブルには、ログ レベルで色分けされた行が表示されます (Kusto エクスプローラと同じです)。

同様のフローを作成するには、以下の手順を実行します。

新しい 'Kusto - クエリを実行して結果を一覧表示' アクションを作成します。

![alt text](./Images/KustoTools-Flow/flow-listresultforhtmltable.png "フローリスト結果のhtmlテーブル")

クエリ結果をループし、HTML テーブルの本文を作成します。 まず、HTML文字列を保持する変数を作成する - 「新しいステップ」をクリックし、「アクションを追加」を選択して「変数」を検索します。 「変数 - 変数の初期化」をクリックします。 文字列変数を次のように初期化します。

![alt text](./Images/KustoTools-Flow/flow-initializevariable.png "フロー初期化変数")

結果のループ - 「新しいステップ」をクリックし、「アクションを追加」を選択します。 「変数」を検索します。 「変数 - 文字列変数に追加」を選択します。 前に初期化した変数名を選択し、クエリ結果を使用して HTML テーブル行を作成します。 クエリ結果を選択すると、「それぞれに適用」が自動的に追加されます。

次の例では、次の if 式を使用して各行のスタイルを定義します。

if(等しい(項目('Apply_to_each')?['レベル']、[警告'、'黄色'、if(Apply_to_eachアイテム)に等しい場合レベル']、'エラー')、'赤'、'白')

![alt text](./Images/KustoTools-Flow/flow-createhtmltableloopcontent.png "フロー作成htmlテーブルループコンテンツ")

最後に、完全な HTML コンテンツを作成します。 「それぞれに適用」の外に新しいアクションを追加します。 次の例では、使用されるアクションは「メールを送信」です。 前の手順の変数を使用して HTML テーブルを定義します。 メールを送信する場合は、[詳細オプションを表示] をクリックし、[HTML] の下で [はい] を選択します。

![alt text](./Images/KustoTools-Flow/flow-customhtmltablemail.png "フローカスタムhtmltablemail")

そして、ここに結果があります:

![alt text](./Images/KustoTools-Flow/flow-customhtmltableresult.png "フローカスタムhtmlテーブル結果")






