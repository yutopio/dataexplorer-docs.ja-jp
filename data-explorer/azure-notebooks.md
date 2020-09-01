---
title: Azure Notebooks を使用して Azure Data Explorer のデータを分析する
description: このトピックでは、Azure Notebook を使用してクエリを作成する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: d41c9e2719ef5139d89986ff5333c03e6e34d26c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872473"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Azure Notebooks を使用して Azure Data Explorer のデータを分析する

[Azure Notebooks](https://notebooks.azure.com/) は、[Jupyter Notebooks](https://jupyter.org/) の作成と共有を簡単にする Azure クラウド サービスです。 Azure Notebooks を使用すると、ドキュメント、コード、およびコードの実行結果を簡単に組み合わせることができます。

> [!Note]
> * 次の手順では、Azure Notebooks 環境で Python クライアントを使用して Azure Data Explorer に対してクエリを実行します。 ただし、[KQLmagic](kqlmagic.md) を使用して Azure Data Explorer に対するクエリを実行することもできます。
> * 一部のユーザーにより、Edge を使用した認証で問題が報告されました。そのような問題が解決されるまでは、別のブラウザーを使用してください。

## <a name="create-a-project"></a>プロジェクトの作成

1. ブラウザーで [Azure Notebooks](https://notebooks.azure.com/) を開き、サインインします。

1. ヘッダーから **[マイ プロジェクト]** タブを選択します。 

    :::image type="content" source="media/azurenotebooks/an-myprojects.png" alt-text="プロジェクト ページ、[マイ プロジェクト] タブ、Microsoft Azure Notebooks、Azure Data Explorer" lightbox="media/azurenotebooks/an-myprojects.png#lightbox":::

1. **[+ 新しいプロジェクト]** を選択します。
    
1. **[新しいプロジェクトの作成]** ダイアログ ボックスで:
    1. **[プロジェクト名]** を入力します。
    1. **[パブリック]** チェック ボックスをオフにします。
        >[!Important]
        > [パブリック] チェックボックスをオフにしないと、プロジェクトはオープンなインターネットで公開されます。
    1. **［作成］** を選択します
    
    ![新しいプロジェクトを作成する](media/azurenotebooks/an-create-new-project-blank.png)

    プロジェクト ID は自動的に作成されます。

## <a name="create-a-notebook"></a>ノートブックを作成する

1. 新しいプロジェクトで、ノートブックを作成します。 ノートブックでは、[サポートされている言語](https://github.com/Azure/azure-kusto-python#minimum-requirements)を使用する必要があります。
ノートブックを作成するには、 **[+ 新規]** を選択し、 **[ノートブック]** を選択します。

    ![新しいノートブックを作成する](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. **[新しいノートブックの作成]** ダイアログ ボックスでノートブックの名前を入力します。

1. **[Python 3.6]** を選択し、 **[New]\(新規\)** を選択します。
    
    ![新しいノートブックを作成する](media/azurenotebooks/an-create-new-notebook.png) 
    
1. プロジェクトで、新しいノートブックを選択します。

    ![新しいノートブックを選択する](media/azurenotebooks/an-select-notebook.png)

    Python カーネルが初期化されるまで待ちます。 塗りつぶされた円アイコンが白抜きの円アイコンに変わったら、先に進むことができます。

    ![カーネルの初期化](media/azurenotebooks/an-python-init-icon.png)

1. システム モジュールをインポートするには、次のコマンドを入力し、 **[実行]** を選択します。
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > セルを実行するには、Shift + Enter キーを押すこともできます。

1.  SDK クラスをインポートするには、次のコマンドを入力し、 **[実行]** を選択します。
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  接続文字列を作成し、Kusto クライアントを起動するには、次のコマンドを入力し、 **[実行]** を選択します。  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. プロンプトで、新しいブラウザー タブで [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開きます。 
   
1. 認証コードを入力し、AAD (@microsoft.com) アカウントにサインインします。 これで、Kusto クライアント `kc` は ID を使用して、Azure Data Explorer に対して認証できるようになりました。

1. ノートブックに戻り、認証の結果を確認します。 

:::image type="content" source="media/azurenotebooks/an-python-commands.png" alt-text="認証の結果の出力、ノートブックのウィンドウ、Microsoft Azure Notebooks、Azure Data Explorer" lightbox="media/azurenotebooks/an-python-commands.png#lightbox":::

## <a name="execute-a-kusto-query"></a>Kusto クエリを実行する

1. Kusto クエリを入力し、 **[実行]** を選択します。 次に例を示します。

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

:::image type="content" source="media/azurenotebooks/an-commands.png" alt-text="[実行] ボタン、ノートブックのウィンドウ、Microsoft Azure Notebooks、Azure Data Explorer" lightbox="media/azurenotebooks/an-commands.png#lightbox":::

## <a name="next-steps"></a>次のステップ

* [Kusto-python GitHub リポジトリ](https://github.com/Azure/azure-kusto-python)
