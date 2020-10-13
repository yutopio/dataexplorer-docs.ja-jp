---
title: Kusto エクスプローラーでの一般的な問題のトラブルシューティング
description: Kusto のインストールと実行に関する一般的な問題について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 9a697cfd37590f0368d5a8f0bacf91d02e1c8725
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003162"
---
# <a name="troubleshooting"></a>トラブルシューティング

このドキュメントでは、Kusto の実行と使用に関する一般的な問題について説明し、ソリューションを提供します。 このドキュメントでは、 [Kusto をリセットする方法](#reset-kustoexplorer)についても説明します。

## <a name="kustoexplorer-fails-to-start"></a>Kusto. エクスプローラーが起動に失敗する

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto. エクスプローラーの起動時または起動後にエラーダイアログが表示される

#### <a name="symptom"></a>症状

起動時に、Kusto. エクスプローラーにエラーが表示されます。 `InvalidOperationException`

#### <a name="possible-solution"></a>考えられる解決策

このエラーは、オペレーティングシステムが破損した、または重要なモジュールの一部が不足していることを示唆する場合があります。
不足または破損しているシステムファイルを確認するには、次の手順に従います。   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto. エクスプローラーは、更新プログラムがない場合でも、常にダウンロードされます。

#### <a name="symptom"></a>症状

Kusto. エクスプローラーを開くたびに、新しいバージョンをインストールするように求められます。 Kusto. エクスプローラーは、インストール済みのバージョンを更新せずに、パッケージ全体をダウンロードします。

#### <a name="possible-solution"></a>考えられる解決策

この現象は、ローカルの ClickOnce ストアが破損した場合に発生する可能性があります。 管理者特権でのコマンドプロンプトで次のコマンドを実行して、ローカルの ClickOnce ストアを消去できます。

> [!IMPORTANT]
> 1. ClickOnce アプリケーションまたはの他のインスタンスがある場合は `dfsvc.exe` 、このコマンドを実行する前にそれらを終了します。
> 1. アプリショートカットに格納されている元のインストール場所にアクセスできる場合は、すべての ClickOnce アプリが次回の実行時に自動的に再インストールされます。 アプリのショートカットは削除されません。

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

[インストールミラー](kusto-explorer.md#installing-kustoexplorer)の1つから、Kusto をもう一度インストールしてみてください。

### <a name="clickonce-error-cannot-start-application"></a>ClickOnce エラー: アプリケーションを開始できません

#### <a name="symptoms"></a>現象

プログラムを起動できず、次のいずれかのエラーが表示されます。 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

エラーの詳細を調べるには `Details` 、次のエラーダイアログをクリックします。

![ClickOnce エラー](./Images/kusto-explorer-troubleshooting/clickonce-err-1.png)

```kusto
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

#### <a name="proposed-solution-steps"></a>提案されたソリューションの手順

1. () を使用して Kusto をアンインストールします `Programs and Features` `appwiz.cpl` 。

1. を実行してから `CleanOnlineAppCache` 、Kusto をもう一度インストールしてみてください。 
    管理者特権のコマンドプロンプトから次のコマンドを実行します。 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    [インストールミラー](kusto-explorer.md#installing-kustoexplorer)のいずれかからもう一度 Kusto をインストールします。

1. アプリケーションがまだ起動しない場合は、ローカルの ClickOnce ストアを削除します。 アプリショートカットに格納されている元のインストール場所にアクセスできる場合は、すべての ClickOnce アプリが次回の実行時に自動的に再インストールされます。 アプリのショートカットは削除されません。

    管理者特権のコマンドプロンプトから次のコマンドを実行します。

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    [インストールミラー](kusto-explorer.md#installing-kustoexplorer)の1つから Kusto をもう一度インストールします。

1. アプリケーションがまだ起動しない場合は、次のようになります。
    1. 一時的な展開ファイルを削除します。
    1. Kusto. Explorer ローカル AppData フォルダーの名前を変更します。

        管理者特権のコマンドプロンプトから次のコマンドを実行します。

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. [インストールミラー](kusto-explorer.md#installing-kustoexplorer)の1つから Kusto をもう一度インストールします。

    1. 管理者特権のコマンドプロンプトから次のように、Kusto. .bak. .bak からの接続を復元します。

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

#### <a name="enabling-clickonce-verbose-logging"></a>ClickOnce の詳細ログを有効にする

1. アプリケーションがまだ起動しない場合は、次のようになります。
    1. 次の下で LogVerbosityLevel string 値1を作成して、[詳細な ClickOnce ログ記録を有効に](https://docs.microsoft.com/visualstudio/deployment/how-to-specify-verbose-log-files-for-clickonce-deployments)します。

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. もう一度再現します。
    1. 詳細出力をに送信し KEBugReport@microsoft.com ます。 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce エラー: コンピューターにセキュリティ上のリスクが生じる可能性があるため、管理者がこのアプリケーションをブロックしました

#### <a name="symptom"></a>症状 
次のいずれかのエラーが発生しても、アプリケーションのインストールに失敗します。
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

#### <a name="solution"></a>解決策

この現象は、別のアプリケーションが ClickOnce 信頼プロンプトの既定の動作をオーバーライドしていることが原因である可能性があります。 
1. 既定の構成設定を表示します。
1. 構成設定をコンピューター上の実際の設定と比較します。
1. [このハウツー記事で](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)説明されているように、必要に応じて構成設定をリセットします。

### <a name="cleanup-application-data"></a>アプリケーションデータのクリーンアップ

前のトラブルシューティングの手順を実行しても Kusto を開始できない場合があります。エクスプローラーを起動するには、ローカルに保存されているデータをクリーニングすることができます。

Kusto エクスプローラーアプリケーションによって保存されるデータについては、「」を参照してください。 `C:\Users\[your username]\AppData\Local\Kusto.Explorer`

> [!NOTE]
> データをクリーニングすると、開いているタブ (回復フォルダー)、保存された接続 (接続フォルダー)、およびアプリケーションの設定 (UserSettings フォルダー) が失われる可能性があります。

## <a name="reset-kustoexplorer"></a>Kusto をリセットします。

必要に応じて、Kusto を完全にリセットできます。 次の手順では、コンピューターから削除されて最初からインストールする必要があるため、Kusto をプログレッシブにリセットする方法について説明します。

1. Windows で、[ **プログラムの変更と削除** ] ( **[プログラムと機能**] とも呼ばれます) を開きます。
1. で始まるすべての項目を選択し `Kusto.Explorer` ます。
1. **[アンインストール]** を選択します。

   この手順でアプリケーションをアンインストールできない場合 (ClickOnce アプリケーションに関する既知の問題)、 [この記事の手順を](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer)参照してください。

1. フォルダーを削除し `%LOCALAPPDATA%\Kusto.Explorer` ます。これにより、すべての接続と履歴が削除されます。

1. フォルダーを削除します。 `%APPDATA%\Kusto` これにより、Kusto. エクスプローラーのトークンキャッシュが削除されます。 すべてのクラスターに対して再認証を行う必要があります。

また、特定のバージョンの Kusto エクスプローラーに戻すこともできます。

1. `appwiz.cpl` を実行します。
1. [ **Kusto エクスプローラー** ] を選択し、[ **アンインストールと変更**] を選択します。
1. [ **アプリケーションを以前の状態に復元する**] を選択します。

## <a name="next-steps"></a>次の手順

* [Kusto エクスプローラーのユーザーインターフェイス](kusto-explorer.md#overview-of-the-user-interface)について説明します。
* [コマンドラインからの Kusto の実行](kusto-explorer-using.md#kustoexplorer-command-line-arguments)について説明します。
* [Kusto クエリ言語 (KQL)](https://docs.microsoft.com/azure/kusto/query/)について学習する
