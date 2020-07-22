---
title: Kusto.Explorer の使用
description: Kusto を使用する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: c95ac178e82e414df41dd5a6d4456f344bb39c2f
ms.sourcegitcommit: 6db94135b9902ad0ea84f9cef00ded8ec0a90fc3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86870126"
---
# <a name="using-kustoexplorer"></a>Kusto.Explorer の使用

Kusto. エクスプローラーは、使いやすいユーザーインターフェイスで Kusto クエリ言語を使用してデータを探索できるデスクトップアプリケーションです。 この記事では、検索とクエリのモードを使用する方法、クエリを共有する方法、およびクラスター、データベース、およびテーブルを管理する方法について説明します。

## <a name="search-mode"></a>検索 + + モード

Search + + モードでは、1つまたは複数のテーブルで検索構文を使用して用語を検索できます。

1. [ホーム] タブの [クエリ] ドロップダウンで、[**検索 + +**] を選択します。
1. **複数のテーブル**を選択します。
1. [**テーブルの選択**] で、検索するテーブルを定義します。
1. [編集] ボックスに検索語句を入力し、[検索]**を選択し**ます。
1. [テーブル/タイムスロット] グリッドのヒートマップには、表示される用語と表示される場所が示されます。

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="+ + Kusto Explorer への検索":::

1. グリッド内のセルを選択し、[**詳細の表示**] を選択すると、結果ペインに関連するエントリが表示されます。

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="Kusto Explorer の検索 + + 結果":::

## <a name="query-mode"></a>クエリモード

Kusto. エクスプローラーには、アドホッククエリの作成、編集、および実行を可能にする強力なスクリプトモードが用意されています。 スクリプトモードには、構文の強調表示と IntelliSense が用意されているため、Kusto クエリ言語の知識をすばやく上げることができます。

このドキュメントでは、Kusto エクスプローラーで基本的なクエリを実行する方法と、クエリにパラメーターを追加する方法について説明します。

## <a name="basic-queries"></a>基本的なクエリ

テーブルログがある場合は、次のことを試してみることができます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

カーソルがこの行にある場合は、灰色で色付けされます。 **F5**キーを押してクエリを実行します。 

次に、クエリの例をいくつか示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="Kusto Explorer の基本的なクエリ":::

[Kusto クエリ言語](https://docs.microsoft.com/azure/kusto/query/)の詳細については、こちらを参照してください。

> [!NOTE]
> クエリ式の空白行は、クエリのどの部分が実行されるかに影響を与える可能性があります。
>
> テキストが選択されていない場合は、クエリまたはコマンドが空の行で区切られていることを前提としています。
> テキストが選択されている場合は、選択したテキストが実行されます。

## <a name="client-side-query-parameterization"></a>クライアント側のクエリのパラメーター化

> [!NOTE]
> Kusto には、次の2種類のクエリ parametrization 手法があります。
> * [統合言語クエリ parametrization](../query/queryparametersstatement.md)は、クエリエンジンの一部として実装されており、プログラムによってサービスをクエリするアプリケーションによって使用されます。 この方法については、このドキュメントでは説明しません。
>
> * 以下で説明するクライアント側クエリ parametrization は、Kusto. エクスプローラーアプリケーションの機能です。 これは、サービスによって実行されるように送信する前に、クエリに対して文字列置換操作を使用することと同じです。 次に示す構文はクエリ言語自体の一部ではなく、Kusto エクスプローラー以外の方法でサービスにクエリを送信するときには使用できません。

複数のクエリまたは複数のタブで同じ値を使用する場合は、使用するすべての場所でその値を変更するのが非常に不便です。 そのため、Kusto. エクスプローラーはクエリパラメーターをサポートしています。 クエリパラメーターは、簡単に再利用できるように、タブ間で共有されます。 パラメーターは、角かっこで示され {} ます。 例: `{parameter1}`

スクリプトエディターでは、クエリパラメーターが強調表示されます。

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="パラメーター化クエリ1":::

既存のクエリパラメーターを簡単に定義および編集できます。


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="パラメーター化クエリの編集2":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="パラメーター化クエリの編集3":::

スクリプトエディターには、既に定義されているクエリパラメーターの IntelliSense もあります。

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="Paramaterized クエリ IntelliSense":::

複数のパラメーターセットを含めることができます ([**パラメーターセット**] コンボボックスに表示されます)。
パラメーターセットの一覧を操作するには、[**新規追加**] または [**現在の削除**] を選択します。

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="パラメーターセットの一覧":::

## <a name="share-queries-and-results"></a>クエリと結果を共有する

Kusto. エクスプローラーでは、クエリと結果を電子メールで共有できます。 ブラウザーでクエリを開いて実行するディープリンクを作成することもできます。

### <a name="share-queries-and-results-by-email"></a>クエリと結果を電子メールで共有する

Kusto. エクスプローラーは、クエリとクエリ結果を電子メールで共有するための便利な方法を提供します。

1. Kusto. エクスプローラーで[クエリを実行](#basic-queries)します。
1. [ホーム] タブの [共有] セクションで、[**クリップボードにエクスポート**] を選択します (または、Ctrl + Shift + C キーを押します)。

    :::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="クリップボードにエクスポート":::

    次のものをクリップボードに貼り付けます。
     * クエリ
     * クエリ結果 (テーブルまたはグラフ)
     * Kusto クラスターとデータベースの接続の詳細
     * 自動的にクエリを再実行するリンク

1. クリップボードの内容を新しい電子メールメッセージに貼り付けます。

    :::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="結果を電子メールで共有":::

### <a name="deep-linking-queries"></a>ディープリンククエリ

ブラウザーで開いたときに Kusto をローカルで開き、指定した Kusto データベースに対して特定のクエリを実行する URI を作成できます。

### <a name="limitations"></a>制限事項

ブラウザーの制限、HTTP プロキシ、および Microsoft Outlook などのリンクを検証するツールによって、クエリの最大文字数は ~ 2000 文字に制限されています。 この制限は、クラスターとデータベース名の長さに依存しているため概数です。 詳細については、「[https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427)」を参照してください。 文字制限に到達する可能性を減らすには、以下の「[短いリンクを取得](#getting-shorter-links)する」を参照してください。

URI の形式は次のとおりです。`https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

例: [https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
この URI は Kusto. エクスプローラーを開き、 `Help` kusto クラスターに接続して、データベースに対して指定されたクエリを実行します `Samples` 。 既に実行されている Kusto. エクスプローラーのインスタンスがある場合は、実行中のインスタンスによって新しいタブが開き、そこでクエリが実行されます。

> [!NOTE] 
> セキュリティ上の理由から、コントロールコマンドではディープリンクが無効になっています。

### <a name="creating-a-deep-link"></a>ディープリンクの作成

ディープリンクを作成する最も簡単な方法は、Kusto エクスプローラーでクエリを作成し、を使用して `Export to Clipboard` クエリ (ディープリンクや結果を含む) をクリップボードにコピーする方法です。 その後、電子メールで共有できます。
        
電子メールにコピーすると、ディープリンクが小さいフォントで表示されます。 次に例を示します。

https://help.kusto.windows.net:443/Samples[[クリックしてクエリを実行](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

最初のリンクを開くと、Kusto. エクスプローラーが開き、クラスターとデータベースのコンテキストが適切に設定されます。
2番目のリンク ( `Click to run query` ) はディープリンクです。 リンクを電子メールメッセージに移動して CTRL + K キーを押すと、実際の URL が表示されます。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>ディープリンクとパラメーター化クエリ

ディープリンクでパラメーター化クエリを使用できます。

1. パラメーター化クエリとして書式設定するクエリを作成します (たとえば、 `KustoLogs | where Timestamp > ago({Period}) | count` )。 
1. 次のように、URI のすべてのクエリパラメーターにパラメーターを指定します。 
    
    `https://<your_cluster>.kusto.windows.net/MyDatabase?
web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

    &lt;Your_cluster &gt; を Azure データエクスプローラークラスター名に置き換えます。


### <a name="getting-shorter-links"></a>短いリンクの取得

クエリが長くなる可能性があります。 クエリが最大長を超える可能性を減らすには、 `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` Kusto クライアントライブラリで使用可能なメソッドを使用します。 この方法では、よりコンパクトな形式のクエリが生成されます。 短い形式は、Kusto. エクスプローラーでも認識されます。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

次の変換を適用することで、クエリがより簡潔になります。

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. エクスプローラーのコマンドライン引数

コマンドライン引数を使用して、スタートアップ時に追加の機能を実行するようにツールを構成します。 たとえば、スクリプトを読み込んでクラスターに接続します。 そのため、コマンドライン引数は、Kusto エクスプローラーの機能に代わるものではありません。

コマンドライン引数は、[ディープリンクをクエリ](#creating-a-deep-link)する場合と同様の方法で、アプリケーションを開くために使用される URL の一部として渡されます。

## <a name="command-line-argument-syntax"></a>コマンドライン引数の構文

Kusto. エクスプローラーでは、次の構文でいくつかのコマンドライン引数がサポートされています (順序は重要です)。

[*LocalScriptFile*][*QueryString*]

* *LocalScriptFile*ローカルコンピューター上のスクリプトファイルの名前を指定します。これには拡張子が必要です `.kql` 。 このようなファイルが存在する場合、このファイルは起動時に自動的に読み込まれます。
* *QueryString*は、HTTP クエリ文字列の書式設定を使用する文字列です。 このメソッドは、次の表で説明するように、追加のプロパティを提供します。

たとえば、というスクリプトファイルで Kusto. エクスプローラーを起動し、 `c:\temp\script.kql` cluster と通信するように構成されている場合は、 `help` `Samples` 次のコマンドを使用します。

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|引数  |説明                                                               |
|----------|--------------------------------------------------------------------------|
|**実行するクエリ**                                                                 |
|`query`   |実行するクエリ (base64 エンコード)。 空の場合は、を使用 `querysrc` します。          |
|`querysrc`|実行するクエリが格納されているファイルまたは blob の URL ( `query` が空の場合)。|
|**Kusto クラスターへの接続**                                                  |
|`uri`     |接続先の Kusto クラスターの接続文字列。                 |
|`name`    |Kusto クラスターへの接続の表示名。                  |
|**接続グループ**                                                                 |
|`path`    |ダウンロードする接続グループファイルの URL (URL エンコード)。             |
|`group`   |接続グループの名前。                                         |
|`filename`|接続グループを保持しているローカルファイル。                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>クラスター、データベース、テーブル、または関数の承認されたプリンシパルの管理

> [!NOTE]
> 承認されたプリンシパルを独自のスコープ内に追加または削除できるのは[管理者](../management/access-control/role-based-authorization.md)だけです。

[[接続] パネル](kusto-explorer.md#connections-tab)でターゲットエンティティを右クリックし、[**クラスターの承認**されたプリンシパルの管理] を選択します。 (このオプションは、[管理] メニューからも選択できます)。

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="承認されたプリンシパルの管理":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="承認されたプリンシパルウィンドウの管理":::

* 新しい承認されたプリンシパルを追加するには、[**プリンシパルの追加**] を選択し、プリンシパルの詳細を入力して、操作を確定します。
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="承認されたプリンシパルの追加":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="承認されたプリンシパルの追加の確認":::

* 既存の承認されたプリンシパルを削除するには、[**プリンシパルの削除**] を選択して操作を確定します。

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="承認されたプリンシパルの削除の確認":::


## <a name="next-steps"></a>次のステップ

* [Kusto. エクスプローラーのキーボードショートカット](kusto-explorer-shortcuts.md)
* [Kusto.Explorer のオプション](kusto-explorer-options.md)
* [Kusto.Explorer のトラブルシューティング](kusto-explorer-troubleshooting.md)

Kusto エクスプローラーのツールとユーティリティの詳細については、以下を参照してください。
* [Kusto. エクスプローラーコードアナライザー](kusto-explorer-code-analyzer.md)
* [Kusto. エクスプローラーコードナビゲーション](kusto-explorer-codenav.md)
* [Kusto. エクスプローラーコードのリファクタリング](kusto-explorer-refactor.md)
* [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
