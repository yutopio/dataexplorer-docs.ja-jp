---
title: UI ディープリンク - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの UI ディープ リンクについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: c9535ced274ccdbe38f8d9a765e98d11e7b381e5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523683"
---
# <a name="ui-deep-links"></a>UI ディープ リンク

REST API は、HTTP`GET`要求が呼び出し元を UI ツールにリダイレクトできるようにするディープ リンク機能を提供します。 たとえば、Kusto.Explorer ツールを開き、特定のクラスターとデータベースに対して自動構成し、特定のクエリを実行してその結果をユーザーに表示する URI を作成できます。

UI は REST API を深くリンクします。

* クラスタ (必須) は、REST API を実装するサービスとして暗黙的に定義されますが、URI クエリ パラメータ`uri`を指定することでオーバーライドできます。

* データベース (オプション) は、URI パスの最初の唯一のフラグメントとして指定されます。 データベースはクエリに必須で、制御コマンドにはオプションです。

* クエリまたはコントロール コマンド (オプション) は、URI クエリ`query`パラメーター または URI`querysrc`クエリ パラメーター (クエリを保持する Web リソースを指す) を使用して指定します。
  `query`は、クエリまたはコントロール コマンド自体のテキストで使用できます (HTTP クエリ パラメーター エンコードを使用してエンコードされます)。 または、クエリまたはコントロール コマンド テキストの gzip の base64 エンコーディングで使用できます (長いクエリを圧縮して既定のブラウザーの URI の長さの制限に合わせることができます)。

* クラスタ接続の名前 (オプション) は、 URI クエリ パラメータ`name`を使用して指定します。

* UI ツールは、オプションの`web`URI クエリ パラメーターを使用して指定します。
  `web=0`は、デスクトップ アプリケーションの Kusto.Explorer を示します。 `web=1`は、アプリケーションを示します。
  `web=2`は、アプリケーション分析に基づく Kusto.WebExplorer の古いバージョンです。 `web=3`は空のプロファイルを持つ Kusto.WebExplorer です (以前に開いていたタブやクラスタは使用できません)。 最後に、`web`クエリ パラメーターを使用`saw=1`して、SAW バージョンの Kusto.Explorer を示すことができます。

リンクの例をいくつか示します。

* `https://help.kusto.windows.net/`: ユーザー エージェント (ブラウザーなど) が要求`GET /`を発行すると、クラスターを照会するように構成されている既定の UI`help`ツールにリダイレクトされます。
* `https://help.kusto.windows.net/Samples`: ユーザー エージェント (ブラウザーなど) が要求`GET /Samples`を発行すると、クラスター`help``Samples`データベースを照会するように構成されている既定の UI ツールにリダイレクトされます。
* `http://help.kusto.windows.net/Samples?query=StormEvents`: ユーザー (ブラウザーなど) が要求を`GET /Samples?query=StormEvents`発行すると、クラスター`help``Samples`データベースを照会してクエリを発行するように構成されている既定の UI ツール`StormEvents`にリダイレクトされます。

> [!NOTE]
> ディープ リンク URI は、リダイレクトに使用される UI ツールによって認証が実行されるため、認証を必要としません。
> HTTP`Authorization`ヘッダーが指定されている場合は、無視されます。

> [!IMPORTANT]
> セキュリティ上の理由から、UI ツールは、ディープ リンクで`query`指定`querysrc`されている場合や指定されている場合でも、コントロール コマンドを自動的に実行しません。

## <a name="deep-linking-to-kustoexplorer"></a>クストーへのディープリンク.エクスプローラ

この REST API は、特定の Kusto エンジン クラスターへの接続を開き、そのクラスターに対してクエリを実行する特別に細工されたスタートアップ パラメーターを使用して Kusto.Explorer デスクトップ クライアント ツールをインストールして実行するリダイレクトを実行します。

* パス: `/` [*データベース名*']
* 動詞：`GET`
* クエリ文字列:`web=0`

> [!NOTE]
> [Kusto.Explorer](../../tools/kusto-explorer.md#deep-linking-queries)を起動するためのリダイレクト URI 構文の詳細については、Kusto.Explorer とのディープ リンクを参照してください。

## <a name="deep-linking-to-kustowebexplorer"></a>クストへのディープリンク.ウェブエクスプローラ

この REST API は、Web アプリケーションである Kusto.WebExplorer へのリダイレクトを実行します。

* パス: `/` [*データベース名*']
* 動詞：`GET`
* クエリ文字列:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>URI でのクエリまたは制御コマンドの指定

URI クエリ文字列パラメーター`query`を指定する場合は、URI クエリ文字列エンコード HTML ルールに従ってエンコードする必要があります。 あるいは、クエリまたは制御コマンドのテキストを gzip で圧縮し、base64 エンコードを使用してエンコードすることもできます。 これにより、より長いクエリやコントロール コマンドを送信できます (後者のエンコード方法の結果 URI が短くなるため)。

## <a name="specifying-the-query-or-control-command-by-indirection"></a>間接指定によるクエリまたは制御コマンドの指定

クエリまたは制御コマンドが非常に長い場合は、gzip/base64 を使用してエンコードしても、ユーザーエージェントの最大 URI 長を超えます。 または、URI クエリ文字列パラメーター`querysrc`が提供され、その値は、クエリまたはコントロール コマンド テキストを保持する Web リソースを指す短い URI です。

たとえば、Azure BLOB ストレージによってホストされるファイルの URI を指定できます。

> [!NOTE]
> ディープ リンクが Web アプリケーション UI ツールに対するリンクである場合は、クエリまたは制御コマンドを提供する Web`querysrc`サービス (つまり、URI を提供`dataexplorer.azure.com`するサービス) が CORS をサポートするように構成されている必要があります。
>
> さらに、そのサービスで認証/承認情報が必要な場合は、URI 自体の一部として提供する必要があります。
>
> たとえば、Azure `querysrc` BLOB ストレージ内の BLOB をポイントする場合は、CORS をサポートするようにストレージ アカウントを構成し、BLOB 自体を公開するか (セキュリティクレームなしでダウンロードできるように)、適切な Azure Storage SAS を URI に追加する必要があります。 CORS の構成は[、Azure ポータル](https://portal.azure.com/)または[Azure ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)から行うことができます。
> [Azure ストレージの CORS サポートを](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)参照してください。

