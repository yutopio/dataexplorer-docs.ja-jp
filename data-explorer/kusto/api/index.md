---
title: Azure Data Explorer API の概要 - Azure Data Explorer
description: この記事では、Azure Data Explorer の API について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2020
ms.openlocfilehash: b9fd03bfd08a31d872ca3c0ef48bd96514e9eb18
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428398"
---
# <a name="azure-data-explorer-api-overview"></a>Azure Data Explorer API の概要

Azure Data Explorer サービスは、次の通信エンドポイントをサポートしています。

1. [REST API](#rest-api) エンドポイント。これを使用して、Azure Data Explorer でデータのクエリと管理を行うことができます。
   このエンドポイントでは、クエリに対する [Kusto クエリ言語](../query/index.md)と[制御コマンド](../management/index.md)がサポートされます。
1. [MS-TDS](#ms-tds) エンドポイント。これは、Microsoft SQL Server 製品で使用される Microsoft の表形式データ ストリーム (TDS) プロトコルのサブセットを実装します。
   このエンドポイントは、クエリのための SQL Server エンドポイントとの通信方法を認識しているツールで役立ちます。
1. Azure サービスの標準的な手段である [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) エンドポイント。 このエンドポイントは、Azure Data Explorer クラスターなどのリソースを管理するために使用されます。

## <a name="rest-api"></a>REST API

Azure Data Explorer サービスと通信する主な手段は、サービスの REST API を使用することです。 この完全に文書化されたエンドポイントにより、呼び出し元は次のことが可能です。

* クエリ データ
* メタデータのクエリと変更
* データの取り込み
* サービスの正常性状態のクエリの実行
* リソースの管理

異なる Azure Data Explorer サービスが、一般に提供されている同じ REST API を使って相互に通信しています。

REST API プロトコルを処理せずにサービスを使用する、[クライアント ライブラリ](client-libraries.md)も多数用意されています。

## <a name="ms-tds"></a>MS-TDS

Azure Data Explorer では Microsoft SQL Server 通信プロトコル (MS-TDS) もサポートされ、T-SQL クエリの実行が限定的にサポートされています。 このプロトコルにより、ユーザーは、よく知られているクエリ構文 (T-SQL) とデータベース クライアント ツール (LINQPad、sqlcmd、Tableau、Excel、Power BI など) を使用して、Azure Data Explorer でクエリを実行できます。

詳細については、[MS-TDS](tds/index.md) を参照してください。

## <a name="client-libraries"></a>クライアント ライブラリ 

Azure Data Explorer には、上記のエンドポイントを使用してプログラムによるアクセスを簡単にするクライアント ライブラリが多数用意されています。

* .NET SDK
* Python SDK
* R
* Java SDK
* Node SDK
* Go SDK
* PowerShell

### <a name="net-framework-libraries"></a>.NET Framework ライブラリ

.NET Framework ライブラリは、Azure Data Explorer の機能をプログラムで呼び出すための推奨方法です。
多数の異なるライブラリが用意されています。

* [Kusto.Data (Kusto クライアント ライブラリ)](./netfx/about-kusto-data.md):データのクエリ、メタデータのクエリ、およびその変更を行うために使用できます。 
   これは、Kusto REST API の上に構築されており、ターゲットの Kusto クラスターに HTTPS 要求を送信します。
* [Kusto.Ingest (Kusto インジェスト ライブラリ)](netfx/about-kusto-ingest.md):`Kusto.Data` を使用し、これを拡張してデータ インジェストを容易にします。

上記のライブラリでは、Azure Storage API や Azure Active Directory API などの Azure API が使用されます。

### <a name="python-libraries"></a>Python ライブラリ

Azure Data Explorer には、呼び出し元がデータ クエリおよび制御コマンドを送信できるようにする Python クライアント ライブラリが用意されています。
詳細については、「[Azure Data Explorer Python SDK](python/kusto-python-client-library.md)」をご覧ください。

### <a name="r-library"></a>R ライブラリ

Azure Data Explorer には、呼び出し元がデータ クエリおよび制御コマンドを送信できるようにする R クライアント ライブラリが用意されています。
詳細については、「[Azure Data Explorer R SDK](r/kusto-r-client-library.md)」をご覧ください。

### <a name="java-sdk"></a>Java SDK

Java クライアント ライブラリには、Java を使用して Azure Data Explorer クラスターに対してクエリを実行する機能が用意されています。 詳細については、「[Azure Data Explorer Java SDK](java/kusto-java-client-library.md)」をご覧ください。

### <a name="node-sdk"></a>Node SDK

Azure Data Explorer Node SDK は、Node LTS (現在は v6.14) と互換性があり、ES6 を使用してビルドされています。
詳細については、「[Azure Data Explorer Node SDK](node/kusto-node-client-library.md)」をご覧ください。

### <a name="go-sdk"></a>Go SDK

Azure Data Explorer の Go クライアント ライブラリには、Go を使用して Azure Data Explorer クラスターへのクエリ、制御、取り込みを実行する機能が用意されています。 詳細については、「[Azure Data Explorer Golang SDK](golang/kusto-golang-client-library.md)」をご覧ください。

### <a name="powershell"></a>PowerShell

Azure Data Explorer .NET Framework ライブラリは、PowerShell スクリプトによって使用できます。 詳細については、[PowerShell からの Azure Data Explorer の呼び出し](powershell/powershell.md)に関する記事をご覧ください。

## <a name="monaco-ide-integration"></a>Monaco IDE の統合

`monaco-kusto` パッケージは、Monaco Web エディターとの統合をサポートしています。
Microsoft によって開発された Monaco エディターは、Visual Studio Code の基礎となります。
詳細については、[monaco-kusto パッケージ](monaco/monaco-kusto.md)に関する記事を参照してください。
