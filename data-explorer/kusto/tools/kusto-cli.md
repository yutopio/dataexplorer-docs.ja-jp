---
title: クスト CLI - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの KUSTO CLI について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7443ad32b78a48f6b35be4f4b680ac6c728aedd2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524244"
---
# <a name="kusto-cli"></a>クストー CLI

Kusto.Cli は、Kusto に要求を送信し、その結果を表示するために使用できるコマンドライン ユーティリティです。 Kusto.Cli は、次のいずれかのモードで実行できます。

* *REPL モード*: Kusto に対して実行するクエリとコマンドを入力し、ツールは結果を表示し、次のユーザー クエリ/コマンドを待機します。
  (「REPL」は「読み取り/eval/印刷/ループ」の略です。

* *実行モード*: ユーザーは、ツールに対してコマンド ライン引数として実行する 1 つ以上のクエリとコマンドを提供します。 これらは自動的に順番に実行され、結果はコンソールに出力されます。 オプションで、すべての入力クエリとコマンドを実行すると、ツールは REPL モードになります。

* *スクリプトモード*: 実行モードに似ていますが、ファイルを介して指定されたクエリとコマンド(「スクリプト」と呼ばれます)。

Kusto.Cli は、通常はコードを記述する必要がある Kusto サービスに対するタスクを自動化するために主に提供されています (C# プログラムや PowerShell スクリプトなど)。

## <a name="getting-the-tool"></a>ツールの取得

Kusto.Cli は NuGet パッケージ`Microsoft.Azure.Kusto.Tools`の一部であり、[ここで](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)ダウンロードできます。
ダウンロードしたパッケージの`tools`フォルダは、ターゲットフォルダに抽出できます。追加のインストールは必要ありません (つまり、xcopy インストール可能です)。



## <a name="running-the-tool"></a>ツールの実行

Kusto.Cli を実行するには、少なくとも 1 つのコマンド ライン引数が必要です。 通常、その引数は、ツールが接続する Kusto サービスへの接続文字列です。[Kusto 接続文字列を](../api/connection-strings/kusto.md)参照してください。 コマンド ライン引数を指定しないツールを実行するか、不明な引数のセットを指定するか、`/help`スイッチを指定してツールを実行すると、ヘルプ メッセージがコンソールに出力されます。

たとえば、次のコマンドを使用して Kusto.Cli を実行し`help`、Kusto サービスに接続し、データベース`Samples`コンテキストをデータベースに設定します。

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 接続文字列の前後に引用符を使用して、接続文字列内のセミコロン ( )`;`などのシェル アプリケーションが解釈されないようにしました。

## <a name="command-line-arguments"></a>コマンド ライン引数

`Kusto.Cli.exe`*接続文字列*[*スイッチ*]

*Connectionstring*
* すべての[Kusto 接続](../api/connection-strings/kusto.md)情報を保持する Kusto 接続文字列。
  既定値は `net.tcp://localhost/NetDefaultDB` です。

`-execute:`*クエリまたはコマンド*
* 指定した場合は、Kusto.Cli を実行モードで実行します。指定されたクエリまたはコマンドが実行されます。 この切り替えは繰り返され、クエリ/コマンドは順次実行されます。
  このスイッチは、 または`-script``-scriptml`と一緒に使用することはできません。

`-keepRunning:`*キープ実行を有効にする*
* `true` (または`false`のいずれかとして) を指定した場合は、すべて`-script`または値が処理された後`-execute`で REPL モードへの切り替えを有効または無効にします。

`-script:`*スクリプトファイル*
* 指定した場合は、Kusto.Cli をスクリプト モードで実行します。指定されたスクリプト ファイルが読み込まれ、その中のクエリまたはコマンドが順次実行されます。
  改行は、以下で説明するように、行がまたは組み合わせで終`&`わる`&&`場合を除き、クエリ/コマンドを区切るために使用されます。
  このスイッチは、 と一`-execute`緒に使用することはできません。

`-scriptml:`*スクリプトファイル*
* 指定した場合は、Kusto.Cli をスクリプト モードで実行します。指定されたスクリプト ファイルが読み込まれ、その中のクエリまたはコマンドが順次実行されます。
  スクリプト ファイル全体は、単一のクエリまたはコマンドと見なされます。
  このスイッチは、 と一`-execute`緒に使用することはできません。

`-echo:`*エコーモードを有効にします。*
* (`true`または`false`) として指定した場合は、エコー モードを有効または無効にします。
  エコー モードが有効な場合、出力内のすべてのクエリまたはコマンドが繰り返されます。

`-transcript:`*トランスクリプトファイル*  
* 指定した場合は、プログラムの出力を*TranscriptFile*に書き込みます。

`-logToConsole:`*コンソールを有効にします。*
* (または`true``false`) として指定した場合は、プログラム出力をコンソールに書き込むかどうかが有効または無効になります。

`-lineMode:`*ラインモードを有効にする*
* 指定した場合、ライン入力モード (デフォルト、または に`true`設定されている場合) とブロック入力モード (に`false`設定されている場合) を切り替えます。 改行がどのように扱われるかを決定するこれら 2 つのモードの説明については、以下を参照してください。

**例**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

コロンと引数値の間にスペースを入れないでください。

## <a name="directives"></a>ディレクティブ

Kusto.Cli は、処理のためにサービスに送信されるのではなく、ツールで実行される多数のディレクティブをサポートしています。

|ディレクティブ                      |説明|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |短いヘルプ メッセージを取得します。|
|`q`<br>`#quit`<br>`#exit`      |ツールを終了します。|
|`#a`<br>`#abort`               |ツールを中止して終了します。|
|`#clip`                        |次のクエリまたはコマンドの結果がクリップボードにコピーされます。|
|`#cls`                         |コンソール画面をクリアします。|
|`#connect`*[接続文字列*]|別の Kusto サービスに接続します (*接続文字列*を省略すると、現在のサービスが表示されます)。|
|`#crp`[*Name*名前`=`[*値*] ]   |クライアント要求プロパティの値を設定します (または、単に表示するか、すべての値を表示します)。|
|`#crp` (`-list` | `-doc`) [*接頭辞*]|クライアント要求のプロパティを (プレフィックスまたはすべてで) 一覧表示します。|
|`#dbcontext`[*データベース名*]  |クエリおよびコマンドで使用される "コンテキスト" データベースを*DatabaseName*に変更します (省略すると、現在のコンテキストが表示されます)。|
|`ke`*テキスト*                    |指定したテキストを実行中の Kusto.Explorer プロセスに送信します。|
|`#loop`*テキスト**のカウント*         |テキストを何度も実行します。|
|`#qp`[*Name*名前`=`[*値*] ]   |クエリ パラメータの値を設定します (または単にクエリ パラメータを表示するか、すべての値を表示します)。 先頭/末尾からの単一引用符/二重引用符はトリムされます。|
|`#save`*ファイル名*             |次のクエリまたはコマンドの結果は、指定された CSV ファイルに保存されます。|
|`#script`*ファイル名*           |指定されたスクリプトを実行します。|
|`#scriptml`*ファイル名*         |指定された複数行スクリプトを実行します。|

## <a name="line-input-mode-and-block-input-mode"></a>ライン入力モードとブロック入力モード

デフォルトでは、Kusto.Cli は**行入力モード**で実行されます: 各改行文字はクエリ/コマンド間の区切り文字として解釈され、その行は直ちに実行のために送信されます。

このモードでは、長いクエリまたはコマンドを複数の行に分割できます。 行の`&`最後の文字 (改行の前) の文字の出現により、Kusto.Cli は次の行を読み続けます。 文字が`&&`行の最後の文字 (改行の前) として表示されると、Kusto.Cli は改行を無視して次の行を読み続けます。

あるいは、Kusto.Cli は**ブロック入力モード**での実行もサポートしています : コマンド ライン`-lineMode:false`スイッチを使用するか`#blockmode`、コマンドを使用して、すべての行が前の行の継続であると見なして、クエリとコマンドが空の入力行のみで区切られるように Kusto.Cli に指示することもできます。

## <a name="comments"></a>説明

Kusto.Cli は、`//`改行を開始する文字列をコメント行として解釈します。 行の残りの部分を無視し、次の行を読み続けます。

## <a name="tool-only-options"></a>ツールのみのオプション

コマンド                        | 結果                                                                            | 現在は
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 有効/無効オプション 'タイミング': 要求にかかった時間を表示します。                    | TRUE
#tableon|#tableoff              | 有効/無効オプション 'tableView': 結果セットを表としてフォーマットする                  | TRUE
#marson|#marsoff                | 有効/無効オプション 'marsView': 2 番目から最後の結果セットを表示します。             | FALSE
#resultson|#resultsoff          | 有効/無効オプション 'outputResultsSet': 結果セットを表示する                 | TRUE
#prettyon|#prettyoff            | 有効/無効オプション 'prettyErrors': エラーから不要な goo を削除します。          | TRUE
#markdownon|#markdownoff        | 有効/無効オプション 'マークダウンビュー': マークダウンとしてテーブルをフォーマット                   | FALSE
#progressiveon|#progressiveoff  | 有効/無効オプション 'progressiveView': プログレッシブ結果を求めて表示する  | FALSE
#linemode|#blockmode            | 有効/無効オプション 'lineMode': 単一行入力モード                          | TRUE

コマンド                              | 結果                                                                                                         | 既定値
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (有効|オプション 'crid' を無効にする: 要求を送信する前に ClientRequestId を表示します)                         | FALSE
#csvheaderson|#csvheadersoff          | (有効|オプション 'csvHeader'を無効にする:CSV出力にヘッダーを含める)                                            | TRUE
#focuson|#focusoff                    | (有効|オプション 'フォーカス'を無効にする:すべての余分な綿毛を削除し、右のものに焦点を当てる)                       | FALSE
#linemode|#blockmode                  | (有効|オプション 'lineMode'を無効にする:単一行入力モード)                                                     | TRUE
#markdownon|#markdownoff              | (有効|オプション 'マークダウンビュー'を無効にする:マークダウンとしてテーブルをフォーマット)                                              | FALSE
#marson|#marsoff                      | (有効|オプション 'marsView'を無効にする: 2 番目から最後の結果セットを表示します)                                        | FALSE
#prettyon|#prettyoff                  | (有効|オプション 'prettyErrors'を無効にする:エラーから不要なgooを削除)                                     | TRUE
#querystreamingon|#querystreamingoff  | (有効|オプション 'queryStreaming'を無効にする:クエリストリーミングエンドポイントを使用する(Kustoチームのみ))                    | FALSE
#resultson|#resultsoff                | (有効|オプション '出力結果セット'を無効にする:結果セットを表示します)                                            | TRUE
#tableon|#tableoff                    | (有効|オプション 'テーブルビュー'を無効にする:結果セットをテーブルとしてフォーマットする)                                             | TRUE
#timeon|#timeoff                      | (有効|オプション 'タイミング'を無効にする:要求にかかった時間を表示します)                                               | TRUE
#typeon|#typeoff                      | (有効|オプション 'typeView'を無効にする:テーブルビューに各列のタイプを表示する(ストリーミング=真を強制する))  | TRUE
#v2protocolon|#v2protocoloff          | (有効|オプション 'v2protocol'を無効にする:v1ではなく、v2クエリプロトコルを使用する)                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>Kusto.Cli を使用して結果を CSV としてエクスポートする

Kusto.Cli は、`#save`**次**のクエリ結果を CSV 形式でローカル ファイルにエクスポートするための特別なクライアント側コマンドをサポートしています。 たとえば、Kusto.Cli を次に呼び出すと、クラスター (データベース`StormEvents`)`Samples`の`help.kusto.windows.net`テーブルから 10 レコードがエクスポートされます。

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Kusto.Cli を使用して、実行中のインスタンスを制御します。

Kusto.Cli に対して、マシン上で実行されている Kusto.Explorer の "プライマリ" インスタンスと通信し、実行するクエリを送信するように指示できます。 これは、Kusto クエリを実行するプログラムで非常に便利ですが、Kusto.Explorer プロセスを何度も何度も開始したくない場合があります。 次の例では、Kusto.Cli を使用して、ヘルプ クラスターを再度クエリを実行します。

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

構文は非常に単純です`#ke`: に続いて空白と実行するクエリを続けます。
クエリは、現在のクラスター/データベースが Kusto.Cli に設定されている Kusto.Explorer のプライマリ インスタンス (存在する場合) に送信されます。