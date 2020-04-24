---
title: Kusto SDK クライアント側トレースの制御または抑制 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで Kusto SDK クライアント側トレースを制御または抑制する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 65964d7e990cdb639bd5bfe319d11874ea3de15d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502739"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Kusto SDK クライアント側トレースの制御または抑制

Kusto クライアント ライブラリは、トレースに共通のプラットフォームを使用します。 プラットフォームは、構築時にトレース リスナー`System.Diagnostics.TraceSource`( ) の既定のセットに接続された多数`System.Diagnostics.Trace.Listeners`のトレース ソース ( ) を使用します。

この 1 つの意味は、アプリケーションにトレース リスナーが関連`System.Diagnostics.Trace`付けられている場合 (たとえば、その`app.config`ファイルを通じて)、Kusto クライアント ライブラリは、それらのリスナーにトレースを出力します。

この動作は、プログラムまたは構成ファイルを通じて抑制または制御できます。

## <a name="suppress-tracing-programmatically"></a>プログラムでトレースを抑制する

Kusto クライアント ライブラリからのトレースをプログラムで抑制するには、関連するライブラリをロードするときに、早い段階で次のコードを呼び出します。

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>構成ファイルを使用したトレースの抑制

構成ファイルを通じて Kusto クライアント ライブラリからのトレースを抑制するには`Kusto.Cloud.Platform.dll.tweaks`、適切な "tweak" が読み取られるようにファイル (`Kusto.Data`ライブラリに含まれている) を変更します。

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(ツイークを有効にするには、マイナス記号を .の値`key`にする必要はありません)

別の方法として、次の操作を行います。

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Kusto クライアント ライブラリのトレースを有効にする方法

Kusto クライアント ライブラリからトレースを有効にするには、アプリケーションの app.config ファイルで .NET トレースを有効にします。 たとえば、アプリケーション`MyApp.exe`が Kusto.Data クライアント ライブラリを使用しているとします。 次に、次`MyApp.exe.config`のファイルを含むように変更すると、アプリケーションを次回起動する時に Kusto.Data トレースが有効になります。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
``` 

これにより、プロセスのディレクトリ`RollingLogs`にあるサブディレクトリ内の CSV ファイルに書き込むトレース リスナーが構成されます。 (もちろん、任意の.NET 互換のトレース リスナー クラスも使用できます)。 

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>AAD クライアント ライブラリ (ADAL) トレースを有効にする方法

Kusto クライアント ライブラリのトレースが有効になると、AAD クライアント ライブラリによって出力されるトレースも (Kusto クライアント ライブラリは自動的に ADAL トレースを設定します)

