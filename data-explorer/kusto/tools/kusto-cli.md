---
title: Kusto CLI-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Kusto CLI について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ab82698054e4bcb851f9f05acdd62af569e0704b
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258115"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto. Cli は、Kusto に要求を送信し、その結果を表示するために使用できるコマンドラインユーティリティです。 Kusto. Cli は、次のいずれかのモードで実行できます。

* *REPL mode*: ユーザーは、Kusto に対して実行するクエリとコマンドを入力し、結果を表示して、次のユーザークエリ/コマンドを待機します。
  ("REPL" は "read/eval/print/loop" を表します)。

* *実行モード*: ユーザーは、ツールのコマンドライン引数として実行する1つ以上のクエリとコマンドを提供します。 これらは自動的に順番に実行され、結果はコンソールに出力されます。 必要に応じて、すべての入力クエリとコマンドを実行すると、REPL モードになります。

* *スクリプトモード*: 実行モードに似ていますが、クエリとコマンドがファイル ("スクリプト" と呼ばれます) で指定されています。

Kusto. Cli は、主にコード (C# プログラムや PowerShell スクリプトなど) を記述する必要がある Kusto サービスに対してタスクを自動化するために用意されています。

## <a name="getting-the-tool"></a>ツールの入手

Kusto. Cli は NuGet パッケージの一部であり `Microsoft.Azure.Kusto.Tools` 、[ここ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)からダウンロードできます。
ダウンロードが完了すると、パッケージの `tools` フォルダーをターゲットフォルダーに抽出できます。追加のインストールは必要ありません (つまり、xcopy によってインストール可能です)。



## <a name="running-the-tool"></a>ツールの実行

Kusto. Cli を実行するには、少なくとも1つのコマンドライン引数が必要です。 通常、この引数は、ツールが接続する必要がある Kusto サービスへの接続文字列です。「 [Kusto 接続文字列」を](../api/connection-strings/kusto.md)参照してください。 コマンドライン引数が指定されていない、または不明な引数セットを使用してツールを実行すると、 `/help` ヘルプメッセージがコンソールに出力されます。

たとえば、次のコマンドを使用して Kusto Cli を実行し、 `help` kusto サービスに接続して、データベースコンテキストをデータベースに設定し `Samples` ます。

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 接続文字列の引用符を使用して、PowerShell などのシェルアプリケーションが `;` 接続文字列内のセミコロン () と同様の文字を解釈できないようにしました。

## <a name="command-line-arguments"></a>コマンド ライン引数

`Kusto.Cli.exe`*ConnectionString* [*スイッチ*]

*文字列*
* すべての Kusto 接続情報を保持する[Kusto 接続文字列](../api/connection-strings/kusto.md)。
  既定値は、`net.tcp://localhost/NetDefaultDB` です。

`-execute:`*QueryOrCommand*
* 指定した場合、実行モードで Kusto. Cli を実行します。指定されたクエリまたはコマンドが実行されています。 このスイッチは繰り返すことができます。この場合、クエリ/コマンドは、表示順に順番に実行されます。
  このスイッチは、またはと共に使用することはできません `-script` `-scriptml` 。

`-keepRunning:`*EnableKeepRunning*
* (またはのいずれかとして) 指定した場合 `true` `false` 、またはのいずれかの `-script` `-execute` 値が処理された後に、REPL モードへの切り替えを有効または無効にします。

`-script:`*ScriptFile*
* 指定した場合、スクリプトモードで Kusto. Cli を実行します。指定されたスクリプトファイルが読み込まれ、クエリまたはコマンドが順番に実行されます。
  改行は、次に説明するように、行がまたはの組み合わせで終了する場合を除き、クエリまたはコマンドの区切りに使用され `&` `&&` ます。
  このスイッチは、と共に使用することはできません `-execute` 。

`-scriptml:`*ScriptFile*
* 指定した場合、スクリプトモードで Kusto. Cli を実行します。指定されたスクリプトファイルが読み込まれ、クエリまたはコマンドが順番に実行されます。
  スクリプトファイル全体は、1つのクエリまたはコマンドと見なされます。
  このスイッチは、と共に使用することはできません `-execute` 。

`-echo:`*EnableEchoMode*
* (またはのいずれかとして) 指定した場合 `true` `false` 、エコーモードを有効または無効にします。
  Echo モードを有効にすると、すべてのクエリまたはコマンドが出力で繰り返されます。

`-transcript:`*TranscriptFile*  
* 指定した場合、プログラムの出力を*TranscriptFile*に書き込みます。

`-logToConsole:`*EnableLogToConsole*
* (またはのいずれかとして) 指定した場合 `true` `false` 、は、コンソールへのプログラム出力の書き込みを有効または無効にします。

`-lineMode:`*EnableLineMode*
* 指定した場合、ライン入力モード (既定値、またはに設定した場合) `true` とブロック入力モード (に設定されている場合) の間で切り替え `false` ます。 これら2つのモードの詳細については、以下を参照してください。これらのモードでは、改行の処理方法が決まります。

**例**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

コロンと引数の値の間にスペースを配置することはできません。

## <a name="directives"></a>ディレクティブ

Kusto. Cli では、処理のためにサービスに送信されるのではなく、ツールで実行される複数のディレクティブをサポートしています。

|ディレクティブ                      |説明|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |短いヘルプメッセージを取得します。|
|`q`<br>`#quit`<br>`#exit`      |ツールを終了します。|
|`#a`<br>`#abort`               |ツール abortively を終了します。|
|`#clip`                        |次のクエリまたはコマンドの結果がクリップボードにコピーされます。|
|`#cls`                         |コンソール画面をクリアします。|
|`#connect`*[ConnectionString*]|別の Kusto サービスに接続します ( *ConnectionString*を省略した場合は、現在のものが表示されます)。|
|`#crp`[*名前*[ `=` *値*]]   |クライアント要求プロパティの値を設定します (または表示するか、すべての値を表示します)。|
|`#crp`( `-list` \| `-doc` ) [*プレフィックス*]|クライアント要求のプロパティを一覧表示します (プレフィックスまたはすべて)。|
|`#dbcontext`[*DatabaseName*]  |クエリおよびコマンドによって使用される "コンテキスト" データベースを*DatabaseName*に変更します (省略した場合、現在のコンテキストが表示されます)。|
|`ke`*テキスト*                    |指定されたテキストを、実行中の Kusto エクスプローラープロセスに送信します。|
|`#loop`*カウント**テキスト*         |テキストを何度も実行します。|
|`#qp`[*名前*[ `=` *値*]]   |クエリパラメーターの値を設定します (またはクエリパラメーターを表示するか、すべての値を表示します)。 先頭/末尾からの単一/二重引用符は切り捨てられます。|
|`#save`*ファイル名*             |次のクエリまたはコマンドの結果は、指定された CSV ファイルに保存されます。|
|`#script`*ファイル名*           |指定されたスクリプトを実行します。|
|`#scriptml`*ファイル名*         |指定された複数行スクリプトを実行します。|

## <a name="line-input-mode-and-block-input-mode"></a>ライン入力モードとブロック入力モード

既定では、Kusto. Cli は**行入力モード**で実行されています。各改行文字はクエリとコマンドの間の区切り記号として解釈され、行は直ちに実行のために送信されます。

このモードでは、長いクエリやコマンドを複数行に分割することができます。 `&`文字が (改行前に) 行の最後の文字として表示されると、Kusto. Cli が次の行の読み取りを続行します。 `&&`(改行前の) 行の最後の文字としての文字の外観は、Kusto. Cli によって改行が無視され、次の行の読み取りが続行されます。

また、Kusto. Cli では、**ブロック入力モード**での実行もサポートされています。コマンドラインスイッチまたはコマンドを使用して、 `-lineMode:false` すべての `#blockmode` 行が前の行の継続であると想定し、クエリとコマンドが空の入力行によって区切られるようにすることができます。

## <a name="comments"></a>説明

Kusto. Cli は、 `//` 新しい行を開始する文字列をコメント行として解釈します。 行の残りの部分を無視し、次の行の読み取りを続行します。

## <a name="tool-only-options"></a>ツールのみのオプション

コマンド                        | 結果                                                                            | 現在は
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 有効/無効オプションの ' タイミング ': 要求された時間を表示します                    | true
#tableon|#tableoff              | enable/disable オプション ' tableView ': 結果セットをテーブルとして書式設定します                  | true
#marson|#marsoff                | 有効/無効オプション ' marsView ': 2 番目の結果セットを表示します             | false
#resultson|#resultsoff          | オプション ' Outputresult Set ' を有効/無効にします。結果セットを表示します                 | true
#prettyon|#prettyoff            | オプションの ' "" の "" の "" の "" エラー "を有効/無効にします。エラーから不要な goo を削除する          | true
#markdownon|#markdownoff        | ' markdownView ' オプションを有効/無効にします。テーブルを MarkDown としてフォーマットします。                   | false
#progressiveon|#progressiveoff  | 有効/無効オプション ' progressiveView ': プログレッシブ結果を確認して表示します  | false
#linemode|#blockmode            | enable/disable オプション ' lineMode ': 単一行入力モード                          | true

コマンド                              | 結果                                                                                                         | Default
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (enable|無効化オプション ' 設定 d ': 要求を送信する前に ClientRequestId を表示します)                         | false
#csvheaderson|#csvheadersoff          | (enable|無効にするオプション ' csvHeaders ': CSV 出力にヘッダーを含める)                                            | true
#focuson|#focusoff                    | (enable|オプション ' focus ' を無効にします。余分な楽しい充実をすべて削除し、右側にフォーカスを移動します)                       | false
#linemode|#blockmode                  | (enable|無効にするオプション ' lineMode ': 単一行入力モード)                                                     | true
#markdownon|#markdownoff              | (enable|オプション ' markdownView ' を無効にします: テーブルを MarkDown としてフォーマットします)                                              | false
#marson|#marsoff                      | (enable|無効にするオプション ' marsView ': 2 番目から最後までの結果セットを表示します)                                        | false
#prettyon|#prettyoff                  | (enable|無効にするオプション ' "のエラー": エラーから不要な goo を削除します)                                     | true
#querystreamingon|#querystreamingoff  | (enable|オプション ' queryStreaming ' を無効にします: queryStreaming エンドポイントを使用します (Kusto チームのみ)                    | false
#resultson|#resultsoff                | (enable|オプション ' Outputresult Set ' を無効にします。結果セットを表示します。                                            | true
#tableon|#tableoff                    | (enable|無効にするオプション ' tableView ': 結果セットをテーブルとして書式設定します)                                             | true
#timeon|#timeoff                      | (enable|無効化オプション ' timing ': 要求された時間を表示します)                                               | true
#typeon|#typeoff                      | (enable|オプション ' typeView ' を無効にします。テーブルビューで各列の種類を表示します (強制的に Streaming = true になります))  | true
#v2protocolon|#v2protocoloff          | (enable|無効化オプション ' v2protocol ': v1 ではなく v2 クエリプロトコルを使用します。                                        | true

## <a name="using-kustocli-to-export-results-as-csv"></a>Kusto. Cli を使用して結果を CSV としてエクスポートする

Kusto. Cli では、特殊なクライアント側コマンドであるをサポートし `#save` ています。これにより、**次**のクエリ結果が CSV 形式でローカルファイルにエクスポートされます。 たとえば、次の Kusto Cli を呼び出すと、 `StormEvents` `help.kusto.windows.net` クラスター (データベース) のテーブルから10個のレコードがエクスポートされます `Samples` 。

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Kusto Cli を使用した Kusto の実行中のインスタンスの制御

Kusto. Cli を使用して、コンピューター上で実行されている Kusto. エクスプローラーの "プライマリ" インスタンスと通信し、実行するクエリを送信することができます。 これは、多数の Kusto クエリを実行するが、Kusto エクスプローラープロセスを再び開始したくないプログラムにとっては非常に便利です。 次の例では、Kusto. Cli を使用して、ヘルプクラスターからもう一度クエリを実行します。

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

構文は非常に単純であり `#ke` 、その後に空白と実行するクエリが続きます。
このクエリは、Kusto. Cli の現在のクラスターまたはデータベースが設定されている Kusto. エクスプローラーのプライマリインスタンスに送信されます (存在する場合)。
