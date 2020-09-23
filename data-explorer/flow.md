---
title: Power Automate に接続する Azure Data Explorer コネクタ (プレビュー)
description: Power Automate に接続する Azure Data Explorer コネクタを使用して、自動的にスケジュール設定されたタスクまたはトリガーされたタスクのフローを作成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 7c40d6b1f62014e8ede6ed3328dd3a3974d41a88
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740305"
---
# <a name="azure-data-explorer-connector-to-power-automate-preview"></a>Power Automate に接続する Azure Data Explorer コネクタ (プレビュー)

Azure Data Explorer Power Automate (以前は Microsoft Flow) コネクタを使用すると、Azure Data Explorer で、[Microsoft Power Automate](https://flow.microsoft.com/) の Flow 機能を使用できます。 Kusto のクエリとコマンドを、スケジュール設定されたタスクまたはトリガーされたタスクの一環として、自動的に実行することができます。

次のようにすることができます。

* テーブルとグラフが含まれる日次レポートを送信する。
* クエリ結果に基づいて通知を設定する。
* クラスターでの制御コマンドのスケジュールを設定する。
* Azure Data Explorer と他のデータベースの間でデータをエクスポートおよびインポートする。 

詳細については、[Azure Data Explorer Power Automate コネクタの使用例](flow-usage.md)に関するページをご覧ください。

##  <a name="sign-in"></a>サインイン 

1. 初めて接続するときに、サインインを求めるプロンプトが表示されます。

1. **[サインイン]** を選択して、資格情報を入力します。

![Azure Data Explorer サインイン プロンプトのスクリーンショット](./media/flow/flow-signin.png)

## <a name="authentication"></a>認証

ユーザー資格情報または Azure Active Directory (Azure AD) アプリケーションを使用して、認証を行うことができます。

> [!Note]
> アプリケーションが [Azure AD アプリケーション](kusto/management/access-control/how-to-provision-aad-app.md)であり、クラスターでクエリを実行する権限が与えられていることを確認します。

1. **[制御コマンドを実行して結果を視覚化する]** で、Flow コネクタの右上にある 3 つのドットを選択します。

   ![[制御コマンドを実行して結果を視覚化する] のスクリーンショット](./media/flow/flow-addconnection.png)

1. **[新しい接続の追加]**  >  **[サービス プリンシパルで接続]** の順に選択します。

   ![[サービス プリンシパルで接続] オプションが指定されている Azure Data Explorer サインイン プロンプトのスクリーンショット](./media/flow/flow-signin.png)

1. 必要な情報を入力します。
   - 接続名:一見して意味の伝わる、新しい接続の名前。
   - クライアント ID: ご自身のアプリケーション ID。
   - クライアント シークレット: ご自身のアプリケーション キー。
   - テナント:アプリケーションを作成した Azure AD ディレクトリの ID。

   ![Azure Data Explorer のアプリケーション認証ダイアログ ボックスのスクリーンショット](./media/flow/flow-appauth.png)

認証が完了すると、新しく追加された接続がフローで使用されていることがわかります。

![アプリケーション認証完了のスクリーンショット](./media/flow/flow-appauthcomplete.png)

今後、このフローは、このアプリケーション資格情報を使用して実行されます。

## <a name="find-the-azure-kusto-connector"></a>Azure Kusto コネクタを検索する

Power Automate コネクタを使用するには、最初にトリガーを追加する必要があります。 トリガーは、一定の期間に基づいて定義することも、直前のフロー アクションへの応答として定義することも可能です。

1. [新しいフローを作成](https://flow.microsoft.com/manage/flows/new)するか、Power Automate ホーム ページで **[マイ フロー]**  >  **[+ 新規]** の順に選択します。

    ![[マイ フロー] と [新規] が強調表示されている、Microsoft Power Automate ホーム ページのスクリーンショット](./media/flow/flow-newflow.png)

1. **[Scheduled--from blank]\(スケジュール済み--空白から作成\)** を選択します。

    ![[Scheduled--from blank]\(スケジュール済み--空白から作成\) が強調表示されている、[新規] ダイアログ ボックスのスクリーンショット](./media/flow/flow-scheduled-from-blank.png)

1. **[予定フローを作成]** に、必要な情報を入力します。
    
    ![[フロー名] オプションが強調表示されている、[予定フローを作成] ページのスクリーンショット](./media/flow/flow-build-scheduled-flow.png)

1. **[作成]**  >  **[新しいステップ]** の順に選択します。
1. 検索ボックスに「*Kusto*」と入力し、 **[Azure Data Explorer]** を選択します。

    ![検索ボックスと Azure Data Explorer が強調表示されている、アクション オプション選択のスクリーンショット](./media/flow/flow-actions.png)

## <a name="flow-actions"></a>フロー アクション

Azure Data Explorer コネクタを開くと、フローに追加可能な 3 つのアクションがあります。 このセクションでは、各アクションの機能とパラメーターについて説明します。

![Azure Data Explorer コネクタ アクションのスクリーンショット](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>制御コマンドを実行して結果を視覚化する

このアクションを使用して、[制御コマンド](kusto/management/index.md)を実行します。

1. クラスターの URL を指定します。 たとえば、「 `https://clusterName.eastus.kusto.windows.net` 」のように入力します。
1. データベースの名前を入力します。
1. 制御コマンドを指定します。
   * フローで使用されるアプリとコネクタから、動的コンテンツを選択する。
   * 値へのアクセス、値の変換や比較のための式を追加する。
1. このアクションの結果をテーブルまたはグラフとしてメールで送信するには、テーブル/グラフの種類を指定します。 これは以下のマシンで行えます。
   * HTML テーブル。
   * 円グラフ。
   * 時間グラフ。
   * 横棒グラフ。

![[繰り返し] ペインの [制御コマンドを実行して結果を視覚化する] のスクリーンショット](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> **[クラスター名]** フィールドに、クラスターの URL を入力します。

### <a name="run-query-and-list-results"></a>クエリを実行し、結果を一覧表示する

> [!Note]
> クエリの先頭がドットの場合 (つまり、[制御コマンド](kusto/management/index.md)の場合) は、[[制御コマンドを実行して結果を視覚化する]](#run-control-command-and-visualize-results) アクションを使用します。

このアクションにより、クエリが Kusto クラスターに送信されます。 この後に追加されるアクションは、クエリの結果の各行を反復処理します。

次の例では、1 分ごとにクエリをトリガーし、そのクエリ結果に基づいてメールを送信さします。 このクエリでは、データベース内の行数をチェックし、行数が 0 よりも大きい場合に限ってメールを送信します。 

![クエリの実行と結果の一覧表示のスクリーンショット](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 複数の行がある列の場合、コネクタは、そこにある各行を実行します。

### <a name="run-query-and-visualize-results"></a>クエリを実行し、結果を視覚化する
        
> [!Note]
> クエリの先頭がドットの場合 (つまり、[制御コマンド](kusto/management/index.md)の場合) は、[[制御コマンドを実行して結果を視覚化する]](#run-control-command-and-visualize-results) アクションを使用します。
        
このアクションを使用して、Kusto クエリの結果をテーブルまたはグラフとして視覚化します。 たとえば、このフローを使用して、日次レポートをメールで受信します。 
    
この例では、クエリの結果が HTML テーブルとして返されます。
            
![クエリの実行と結果の視覚化のスクリーンショット](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> **[クラスター名]** フィールドに、クラスターの URL を入力します。

## <a name="email-kusto-query-results"></a>Kusto クエリの結果をメールで送信する

任意のフローにステップを含めて、レポートを、任意のメールアドレスにメールで送信できます。 

1. 新しいステップをフローに追加するには、 **[+ 新規のステップ]** を選択します。
1. 検索ボックスに「*Office 365*」と入力し、 **[Office 365 Outlook]** を選択します。
1. **[メールの送信 (V2)]** を選択します。
1. レポートの送信先のメール アドレスを入力します。
1. メールの件名を入力します。
1. **[コード ビュー]** . を選択します。
1. **[本文]** フィールドにカーソルをあわせ、 **[動的コンテンツの追加]** を選択します。
1. **BodyHtml**を選択します。
    ![[本文] フィールドと BodyHtml が強調表示されている、[電子メールの送信] ダイアログ ボックスのスクリーンショット](./media/flow/flow-send-email.png)
1. **[詳細オプションの表示]** 　を選択します。
1. **[Attachments Name -1]\(添付ファイル名 - 1\)** で、 **[Attachment Name]\(添付ファイル名\)** を選択します。
1. **[Attachments Content]\(添付ファイルのコンテンツ\)** で、 **[Attachment Content]\(添付ファイルのコンテンツ\)** を選択します。
1. 必要に応じて、添付をさらに追加します。 
1. 必要に応じて、重要度レベルを設定します。
1. **[保存]** を選択します。

![[Attachments Name]\(添付ファイル名\)、[Attachments Content]\(添付ファイルのコンテンツ\)、および [保存] が強調表示されている、[電子メールの送信] ダイアログ ボックスのスクリーンショット](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>フローが成功したかを確認する

フローが成功したかを確認するには、フローの実行履歴を確認してください。
1. [Microsoft Power Automate ホーム ページ](https://flow.microsoft.com/) 移動します。
1. メインメニューから、[[マイ フロー](https://flow.microsoft.com/manage/flows)] を選択します。
   
   ![[マイ フロー] が強調表示されている、Microsoft Power Automate メイン メニューのスクリーンショット](./media/flow/flow-myflows.png)

1. 調べたいフローの行で [その他のコマンド] アイコンを選択し、 **[実行履歴]** を選択します。

    ![[実行履歴] が強調表示されている [マイ フロー] タブのスクリーンショット](./media/flow//flow-runhistory.png)

    実行されたすべてのフローの開始時刻、期間、および状態に関する情報が一覧表示されます。
    ![実行履歴の結果ページのスクリーンショット](./media/flow/flow-runhistoryresults.png)

    フローの詳細については、 **[マイ フロー](https://flow.microsoft.com/manage/flows)** で、調べたいフローを選択します。

    ![実行履歴の完全な結果ページのスクリーンショット](./media/flow/flow-fulldetails.png) 

実行が失敗した理由を確認するには、[実行の開始時刻] を選択します。 フローが表示され、失敗したフローのステップが赤色の感嘆符で示されます。 失敗したステップを展開し、詳細を表示します。 右側の **[詳細]** ウィンドウにはエラー情報が示されています。この情報に基づいてトラブルシューティングを行うことができます。

![エラー ページのスクリーンショット](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>タイムアウト例外

フローが 7 分以上実行されると、そのフローは失敗し、「リクエスト タイムアウト」の例外が返されます。
    
![フローの要求タイムアウト例外エラーのスクリーンショット](./media/flow/flow-requesttimeout.png)

タイムアウトの問題を解決するには、クエリを効率化して実行速度を上げるか、チャンクに分割します。 各チャンクは、クエリの別のパートで実行可能です。 詳細については、「[クエリのベスト プラクティス](kusto/query/best-practices.md)」を参照してください。

Azure Data Explorer で、同じクエリが正常に実行される場合があります。この場合、時間の制限はなく、変更も可能です。

## <a name="limitations"></a>制限事項

* クライアントに返される結果は、500,000レコードが上限です。 これらのレコードの合計メモリの上限は 64 MB、実行時間の上限は 7 分です。
* コネクタは、[フォーク](kusto/query/forkoperator.md)演算子と[ファセット](kusto/query/facetoperator.md)演算子をサポートしていません。
* Microsoft Edge または Google Chrome で Flow を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

[Azure Kusto Logic App コネクタ](kusto/tools/logicapps.md)について確認します。この方法を使用して、Kusto のクエリとコマンドを、スケジュール設定されたタスクまたはトリガーされたタスクの一環として自動的に実行することもできます。
