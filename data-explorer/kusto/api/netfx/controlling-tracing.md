---
title: SDK クライアント側のトレースを制御または非表示にする kusto-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの Kusto SDK クライアント側トレースの制御と抑制について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: 2224fe28c7f0088ac1a16cdee4d452e354ff0800
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324756"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>Kusto SDK のクライアント側トレースの制御と抑制

Kusto クライアントライブラリは、トレースに共通のプラットフォームを使用します。 このプラットフォームでは、多数のトレースソース () が使用され、 `System.Diagnostics.TraceSource` その構築時にそれぞれがトレースリスナー () の既定のセットに接続され `System.Diagnostics.Trace.Listeners` ます。

アプリケーションに既定のインスタンスに関連付けられているトレースリスナー (ファイルなど) がある場合 `System.Diagnostics.Trace` `app.config` 、Kusto クライアントライブラリはこれらのリスナーにトレースを出力します。

トレースは、プログラムで、または構成ファイルを使用して、抑制または制御することができます。

## <a name="suppress-tracing-programmatically"></a>プログラムによるトレースの抑制

プログラムによって Kusto クライアントライブラリからのトレースを抑制するには、関連するライブラリを読み込むときに、次のコードを呼び出します。

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>構成ファイルを使用したトレースの抑制 

構成ファイルを使用して Kusto クライアントライブラリからのトレースを抑制するには、ファイル `Kusto.Cloud.Platform.dll.tweaks` (ライブラリに含まれています) を変更し `Kusto.Data` ます。

```xml
    //Overrides the default trace verbosity level
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> 調整を有効にするには、の値にマイナス記号を付けることはできません。 `key`

代替手段は次のとおりです。

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>Kusto クライアントライブラリのトレースを有効にする

Kusto クライアントライブラリからのトレースを有効にするには、アプリケーションの *app.config ファイル* で .net トレースを有効にします。 たとえば、アプリケーションが `MyApp.exe` Kusto. Data クライアントライブラリを使用しているとします。 ファイル *MyApp.exe.config* を次のように変更すると、次にアプリケーションを起動するときにトレースが有効になり `Kusto.Data` ます。

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

このコードでは、 *Rollogs* という名前のサブディレクトリにある CSV ファイルに書き込むトレースリスナーを構成します。 サブディレクトリは、プロセスのディレクトリにあります。

> [!NOTE]
> いつ.NET 互換のトレースリスナークラスも使用できます

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>Azure AD クライアントライブラリ (ADAL) のトレースを有効にする

Kusto クライアントライブラリのトレースが有効になると、は Azure AD クライアントライブラリによってトレースされます。 Kusto クライアントライブラリは、ADAL トレースを自動的に構成します。
