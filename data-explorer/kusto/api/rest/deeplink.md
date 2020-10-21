---
title: UI ディープリンク-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの UI ディープリンクについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 412e489365daabfdde7de8cd61e398100f4bc3ef
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337399"
---
# <a name="ui-deep-links"></a>UI ディープリンク

REST API には、HTTP `GET` 要求によって呼び出し元を UI ツールにリダイレクトできるディープリンク機能が用意されています。 たとえば、Kusto エクスプローラーツールを開き、特定のクラスターおよびデータベース用に自動構成し、特定のクエリを実行し、その結果をユーザーに表示する URI を作成できます。

UI ディープリンク REST API:

* クラスター (必須) は、通常、REST API を実装するサービスとして暗黙的に定義されますが、URI クエリパラメーターを指定することによってオーバーライドでき `uri` ます。

* データベース (省略可能) は、URI パスの最初の唯一のフラグメントとして指定されています。 クエリでは、データベースは必須であり、制御コマンドでは省略可能です。

* クエリまたは制御コマンド (オプション) は、URI クエリパラメーターを使用して指定する `query` か、 `querysrc` (クエリを保持する web リソースを参照する) uri クエリパラメーターを使用して指定します。
  `query` クエリまたはコントロールコマンド自体のテキストで使用できます (HTTP クエリパラメーターエンコーディングを使用してエンコードされます)。 または、クエリまたは制御コマンドテキストの gzip の base64 エンコードで使用することもできます (長いクエリを圧縮して、既定のブラウザー URI の長さの制限に合わせることができるようにします)。

* クラスター接続の名前 (省略可能) は、URI クエリパラメーターを使用して指定し `name` ます。

* UI ツールは、省略可能な URI クエリパラメーターを使用して指定し `web` ます。
  `web=0` デスクトップアプリケーション Kusto. エクスプローラーを示します。 `web=1` Kusto. WebExplorer web アプリケーションを示します。
  `web=2` は、以前のバージョンの Kusto. WebExplorer (Application Insights Analytics に基づく) です。 `web=3` は、空のプロファイルを持つ Kusto. WebExplorer です (以前に開いていたタブやクラスターは使用できません)。 最後に、表示 `web` されて `saw=1` いる Kusto エクスプローラーを示すために、クエリパラメーターをに置き換えることができます。

リンクの例をいくつか次に示します。

* `https://help.kusto.windows.net/`: ユーザーエージェント (ブラウザーなど) が要求を発行すると、クラスターに対し `GET /` てクエリを実行するように構成された既定の UI ツールにリダイレクトされ `help` ます。
* `https://help.kusto.windows.net/Samples`: ユーザーエージェント (ブラウザーなど) が要求を発行すると、 `GET /Samples` クラスターデータベースに対してクエリを実行するように構成された既定の UI ツールにリダイレクトされ `help` `Samples` ます。
* `http://help.kusto.windows.net/Samples?query=StormEvents`: ユーザー (ブラウザーなど) が要求を発行すると、 `GET /Samples?query=StormEvents` クラスターデータベースに対してクエリを実行するように構成された既定の UI ツールにリダイレクトされ、 `help` クエリを発行し `Samples` `StormEvents` ます。

> [!NOTE]
> リダイレクトに使用される UI ツールによって認証が実行されるため、ディープリンク Uri は認証を必要としません。
> `Authorization`HTTP ヘッダーが指定されている場合は無視されます。

> [!IMPORTANT]
> セキュリティ上の理由から、 `query` `querysrc` ディープリンクでまたはが指定されている場合でも、UI ツールはコントロールコマンドを自動的に実行しません。

## <a name="deep-linking-to-kustoexplorer"></a>Kusto. エクスプローラーへのディープリンク

この REST API は、特定の Kusto エンジンクラスターへの接続を開き、そのクラスターに対してクエリを実行する特別に細工されたスタートアップパラメーターを使用して、Kusto エクスプローラーデスクトップクライアントツールをインストールして実行するリダイレクトを実行します。

* パス: `/` [*DatabaseName*']
* 助動詞 `GET`
* クエリ文字列: `web=0`

> [!NOTE]
> Kusto を起動するためのリダイレクト URI 構文の詳細については、「 [Kusto によるディープリンク](../../tools/kusto-explorer-using.md#deep-linking-queries) 」を参照してください。

## <a name="deep-linking-to-kustowebexplorer"></a>Kusto へのディープリンク (WebExplorer)

この REST API は、web アプリケーションである Kusto. WebExplorer へのリダイレクトを実行します。

* パス: `/` [*DatabaseName*']
* 助動詞 `GET`
* クエリ文字列: `web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>URI でのクエリまたは制御コマンドの指定

URI クエリ文字列パラメーターを指定する場合 `query` は、uri クエリ文字列のエンコード HTML ルールに従ってエンコードする必要があります。 または、クエリまたはコントロールコマンドのテキストを gzip で圧縮し、base64 エンコードを使用してエンコードすることもできます。 これにより、より長いクエリまたは制御コマンドを送信することができます (後者のエンコード方法では Uri が短くなります)。

## <a name="specifying-the-query-or-control-command-by-indirection"></a>間接的にクエリまたは制御コマンドを指定する

クエリまたは制御コマンドが非常に長い場合、gzip/base64 を使用してエンコードした場合でも、ユーザーエージェントの URI の最大長を超えることはありません。 または、URI クエリ文字列パラメーターを `querysrc` 指定します。その値は、クエリまたはコントロールのコマンドテキストを保持する web リソースを指す短い uri です。

たとえば、Azure Blob Storage によってホストされるファイルの URI を指定できます。

> [!NOTE]
> ディープリンクが web アプリケーション UI ツールに対して実行される場合、クエリまたは制御コマンドを提供する web サービス (つまり、URI を提供するサービス) は、 `querysrc` の CORS をサポートするように構成する必要があり `dataexplorer.azure.com` ます。
>
> さらに、そのサービスで認証/承認情報が必要な場合は、URI 自体の一部として指定する必要があります。
>
> たとえば、が `querysrc` Azure Blob Storage の blob を指している場合、CORS をサポートするようにストレージアカウントを構成し、blob 自体をパブリック (セキュリティ要求なしでダウンロードできるようにするため) にするか、適切な AZURE STORAGE SAS を URI に追加する必要があります。 CORS の構成は、 [Azure portal](https://portal.azure.com/) または [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)から行うことができます。
> [Azure Storage での CORS のサポートを](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)参照してください。