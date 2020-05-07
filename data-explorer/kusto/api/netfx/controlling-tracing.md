---
title: Kusto SDK クライアント側のトレースの制御または抑制-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーで Kusto SDK クライアント側のトレースを制御または抑制する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: cbda69063e3b1a20549dbadb4641fc9fd3f51f57
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862056"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Kusto SDK クライアント側のトレースの制御または抑制

Kusto クライアントライブラリは、トレースに共通のプラットフォームを使用します。 このプラットフォームでは、多数のトレースソース (`System.Diagnostics.TraceSource`) が使用されており、それぞれが構築時`System.Diagnostics.Trace.Listeners`に既定のトレースリスナー () に接続されています。

この1つの意味は、アプリケーションに既定`System.Diagnostics.Trace`のインスタンスに関連付けられたトレースリスナーがある場合`app.config` (たとえば、ファイルを介して)、Kusto クライアントライブラリによってそれらのリスナーにトレースが出力されることです。

この動作は、プログラムまたは構成ファイルを使用して、抑制または制御することができます。

## <a name="suppress-tracing-programmatically"></a>プログラムによるトレースの抑制

プログラムによって Kusto クライアントライブラリからのトレースを抑制するには、関連するライブラリを読み込むときに、次のコードを事前に呼び出します。

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>構成ファイルを使用したトレースの抑制

構成ファイルを使用して Kusto クライアントライブラリからのトレースを抑制するに`Kusto.Cloud.Platform.dll.tweaks`は、適切な "微`Kusto.Data`調整" が読み込まれるようにファイル (ライブラリに含まれています) を変更します。

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(微調整を有効にするには、の`key`値にマイナス記号を付ける必要があります)。

別の方法として、次の操作を行うこともできます。

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Kusto クライアントライブラリのトレースを有効にする方法

Kusto クライアントライブラリからのトレースを有効にするには、アプリケーションの app.config ファイルで .NET トレースを有効にします。 たとえば、アプリケーション`MyApp.exe`が Kusto. Data クライアントライブラリを使用していると仮定します。 次に、ファイル`MyApp.exe.config`を次のように変更すると、アプリケーションの次回起動時に Kusto. Data tracing が有効になります。

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

これにより、プロセスのディレクトリにあるという名前`RollingLogs`のサブディレクトリ内の CSV ファイルに書き込むトレースリスナーが構成されます。 (もちろん、NET 互換のトレースリスナークラスも使用できます)。

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>AAD クライアントライブラリ (ADAL) のトレースを有効にする方法

Kusto クライアントライブラリのトレースを有効にすると、AAD クライアントライブラリによって生成されるトレース (Kusto クライアントライブラリは ADAL トレースを自動的に構成します)
