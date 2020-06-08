---
title: Kusto CLI-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto CLI について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: adc606c787ab20f228a9fbd132d1b82660aa57c6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512488"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto. Cli は、Kusto に要求を送信し、結果を表示するために使用されるコマンドラインユーティリティです。 次のいずれかのモードで実行できます。

* *REPL mode*: ユーザーがクエリとコマンドを入力すると、結果が表示され、次のユーザークエリ/コマンドが待機します。
  ("REPL" は "read/eval/print/loop" を表します)。

* *実行モード*: ユーザーは、コマンドライン引数として実行する1つ以上のクエリとコマンドを入力します。 引数は順番に自動的に実行され、結果はコンソールに出力されます。 必要に応じて、すべての入力クエリとコマンドを実行した後で、このツールを REPL モードにします。

* *スクリプトモード*: execute モードに似ていますが、"スクリプト" ファイルによって指定されたクエリとコマンドを使用します。

Kusto. Cli は主に、コードの記述が必要な Kusto サービスに対してタスクを自動化するために用意されています。 たとえば、C# プログラムや PowerShell スクリプトなどです。

## <a name="get-the-tool"></a>ツールの入手

Kusto. Cli は、 `Microsoft.Azure.Kusto.Tools` [ダウンロードできる](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)NuGet パッケージの一部です。
ダウンロードが完了したら、パッケージの `tools` フォルダーをターゲットフォルダーに抽出します。 Xcopy でインストール可能なため、追加のインストールは必要ありません。

## <a name="run-the-tool"></a>ツールを実行します。

Kusto. Cli を実行するには、少なくとも1つのコマンドライン引数が必要です。 通常、この引数は、ツールが接続する必要がある Kusto サービスへの接続文字列です。
詳細については、「 [Kusto 接続文字列](../api/connection-strings/kusto.md)」を参照してください。 コマンドライン引数を指定せずにツールを実行した場合、引数のセットが不明な場合、またはスイッチを使用した場合は `/help` 、コンソールにヘルプメッセージが表示されます。

たとえば、次のコマンドを使用して、Kusto Cli を実行します。 コマンドは `help` kusto サービスに接続し、データベースコンテキストをデータベースに設定し `Samples` ます。

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 接続文字列の前後に二重引用符を使用して、PowerShell などのシェルアプリケーションがセミコロン () と同様の文字を誤って解釈しないようにし `;` ます。

## <a name="command-line-arguments"></a>コマンド ライン引数

`Kusto.Cli.exe`*ConnectionString* [*スイッチ*]

*文字列*
* すべての Kusto 接続情報を保持する[Kusto 接続文字列](../api/connection-strings/kusto.md)。
  既定値は `net.tcp://localhost/NetDefaultDB` です。

`-execute:`*QueryOrCommand*
* 指定した場合、実行モードで Kusto. Cli を実行し、指定されたクエリまたはコマンドが実行されます。 このスイッチは繰り返すことができ、クエリやコマンドは、表示順に順番に実行されます。
  このスイッチは、またはと共に使用することはできません `-script` `-scriptml` 。

`-keepRunning:`*EnableKeepRunning*
* 指定した場合、またはとして、またはのいずれか `true` `false` の `-script` `-execute` 値が処理された後に、REPL モードを有効または無効にします。

`-script:`*ScriptFile*
* 指定した場合、スクリプトモードで Kusto. Cli を実行します。 指定されたスクリプトファイルが読み込まれ、クエリまたはコマンドが順番に実行されます。
  次に説明するように、行の後にまたはの組み合わせを使用する場合を除き、クエリやコマンドを区切るには、改行を使用し `&` `&&` ます。
  このスイッチは、と共に使用することはできません `-execute` 。

`-scriptml:`*ScriptFile*
* 指定した場合、スクリプトモードで Kusto. Cli を実行します。 指定されたスクリプトファイルが読み込まれ、クエリまたはコマンドが順番に実行されます。
  スクリプトファイル全体は、1つのクエリまたはコマンドと見なされます。
  このスイッチは、と共に使用することはできません `-execute` 。

`-echo:`*EnableEchoMode*
* 指定した場合、またはのいずれかとして、 `true` `false` エコーモードを有効または無効にします。
  Echo モードを有効にすると、すべてのクエリまたはコマンドが出力で繰り返されます。

`-transcript:`*TranscriptFile*  
* 指定した場合、プログラムの出力を*TranscriptFile*に書き込みます。

`-logToConsole:`*EnableLogToConsole*
* 指定した場合、またはのいずれかとして、 `true` `false` コンソールでのプログラム出力の表示を有効または無効にします。

`-lineMode:`*EnableLineMode*
* 指定した場合、既定のライン入力モード (に設定されている場合) `true` とブロック入力モード (に設定されている場合) の間で切り替え `false` ます。 これら2つのモードの詳細については、以下を参照してください。これらのモードでは、改行の処理方法が決まります。

**例**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

> [!NOTE] 
> コロンと引数の値の間にスペースを配置することはできません。

## <a name="directives"></a>ディレクティブ

Kusto. Cli は、処理のためにサービスに送信するのではなく、複数のディレクティブをツールで実行します。

|ディレクティブ                      |説明|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |短いヘルプメッセージを取得する|
|`q`<br>`#quit`<br>`#exit`      |ツールを終了する|
|`#a`<br>`#abort`               |ツール abortively を終了します。|
|`#clip`                        |次のクエリまたはコマンドの結果がクリップボードにコピーされます|
|`#cls`                         |コンソール画面をクリアする|
|`#connect`*[ConnectionString*]|別の Kusto サービスに接続します ( *ConnectionString*を省略した場合、現在のものが表示されます)|
|`#crp`[*名前*[ `=` *値*]]   |クライアント要求プロパティの値を設定するか、またはそれを表示するか、すべての値を表示します。|
|`#crp`( `-list` \| `-doc` ) [*プレフィックス*]|クライアント要求プロパティ、プレフィックス別、またはすべてを一覧表示します。|
|`#dbcontext`[*DatabaseName*]  |クエリおよびコマンドによって使用される "コンテキスト" データベースを*DatabaseName*に変更します。 省略した場合、現在のコンテキストが表示されます。|
|`ke`*テキスト*                    |指定されたテキストを、実行中の Kusto エクスプローラープロセスに送信します。|
|`#loop`*カウント**テキスト*         |テキストを何度も実行します|
|`#qp`[*名前*[ `=` *値*]]   |クエリパラメーターの値を設定するか、またはその値を表示するか、すべての値を表示します。 先頭/末尾に一重引用符または二重引用符を使用すると、切り捨てられます|
|`#save`*ファイル名*             |次のクエリまたはコマンドの結果は、指定された CSV ファイルに保存されます。|
|`#script`*ファイル名*           |指定されたスクリプトを実行します。|
|`#scriptml`*ファイル名*         |指定された複数行スクリプトを実行します|

## <a name="line-input-mode-and-block-input-mode"></a>ライン入力モードとブロック入力モード

既定では、Kusto. Cli は**ライン入力モード**で実行されます。 各改行文字は、クエリとコマンドの間の区切り記号として解釈され、行は直ちに実行のために送信されます。

このモードでは、長いクエリまたはコマンドを複数行に分割できます。 `&`改行前の行の最後の文字としての文字は、Kusto. Cli によって次の行の読み取りを続行します。 改行 `&&` 前の行の最後の文字としての文字は、Kusto. Cli によって改行が無視され、次の行の読み取りが続行されます。

Kusto. Cli は、**ブロック入力モード**での実行もサポートしています。 コマンドラインスイッチを使用するか、ディレクティブを使用することにより、 `-lineMode:false` `#blockmode` すべての行が前の行の継続であると想定して、クエリとコマンドが空の入力行のみで区切られるようにすることができます。

## <a name="comments"></a>コメント

Kusto. Cli は、 `//` 新しい行を開始する文字列をコメント行として解釈します。 行の残りの部分を無視し、次の行の読み取りを続行します。

## <a name="tool-only-options"></a>ツールのみのオプション

コマンド                        | 結果                                                                            | 現在は
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 有効/無効オプション `timing` : 要求された時間を表示します                    | true
#tableon|#tableoff              | enable/disable オプション `tableView` : 結果セットをテーブルとして書式設定する                  | true
#marson|#marsoff                | 有効/無効オプション `marsView` : 2 番目の結果セットを表示します。          | false
#resultson|#resultsoff          | 有効/無効オプション `outputResultsSet` : 結果セットを表示します。                 | true
#prettyon|#prettyoff            | 有効/無効オプション `prettyErrors` : クリーンアップエラー                             | true
#markdownon|#markdownoff        | enable/disable オプション `markdownView` : テーブルを MarkDown としてフォーマットします。                   | false
#progressiveon|#progressiveoff  | 有効/無効オプション `progressiveView` : プログレッシブ結果を確認して表示します  | false
#linemode|#blockmode            | enable/disable オプション `lineMode` : 単一行入力モード                          | true

コマンド                              | 結果                                                                                                         | Default
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (enable|disable オプション `crid` : 要求を送信する前に ClientRequestId を表示します。                          | false
#csvheaderson|#csvheadersoff          | (enable|無効化オプション `csvHeaders` : CSV 出力にヘッダーを含める)                                            | true
#focuson|#focusoff                    | (enable|disable オプション `focus` : すべての余分な楽しい充実を削除し、右側にフォーカスを移動します)                        | false
#linemode|#blockmode                  | (enable|disable オプション `lineMode` : 単一行入力モード)                                                      | true
#markdownon|#markdownoff              | (enable|disable オプション `markdownView` : MarkDown としてのテーブルのフォーマット)                                              | false
#marson|#marsoff                      | (enable|disable オプション `marsView` : 2 番目から最後の結果セットを表示します。                                      | false
#prettyon|#prettyoff                  | (enable|無効にするオプション `prettyErrors` : エラーのクリーンアップ                                                        | true
#querystreamingon|#querystreamingoff  | (enable|disable オプション `queryStreaming` : queryStreaming エンドポイントを使用します (Kusto チームのみ)                    | false
#resultson|#resultsoff                | (enable|disable オプション `outputResultsSet` : 結果セットを表示します。                                            | true
#tableon|#tableoff                    | (enable|disable オプション `tableView` : 結果セットをテーブルとして書式設定します。                                              | true
#timeon|#timeoff                      | (enable|無効化オプション `timing` : 要求にかかった時間を表示します。                               | true
#typeon|#typeoff                      | (enable|disable option `typeView` : テーブルビューで各列の種類を表示します。 強制的に Streaming = true)| true
#v2protocolon|#v2protocoloff          | (enable|disable オプション `v2protocol` : v1 ではなく v2 クエリプロトコルを使用します。                                        | true

## <a name="use-kustocli-to-export-results-as-csv"></a>Kusto. Cli を使用して結果を CSV としてエクスポートする

Kusto. Cli には、 `#save` **次**のクエリ結果を CSV 形式でローカルファイルにエクスポートする特別なクライアント側コマンドがあります。 たとえば、次の行は、 `StormEvents` テーブルから `help.kusto.windows.net` クラスター (データベース) に10個のレコードをエクスポートし `Samples` ます。

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="use-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Kusto Cli を使用して、実行中の Kusto エクスプローラーのインスタンスを制御します。

Kusto. Cli を使用して、コンピューター上で実行されている Kusto. エクスプローラーの "プライマリ" インスタンスと通信し、クエリを送信するように指示できます。 このメカニズムは、多数のクエリを実行するが、Kusto エクスプローラープロセスを繰り返し開始したくないプログラムに便利です。 次の例では、Kusto. Cli を使用して、ヘルプクラスターに対してクエリを実行します。

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

構文は単純なもので `#ke` 、その後に空白文字が続き、クエリが実行されます。
クエリは、Kusto. Cli のプライマリインスタンスに送信されます。このインスタンスが存在する場合は、現在のクラスター/データベースが Kusto Cli に設定されます。
 
