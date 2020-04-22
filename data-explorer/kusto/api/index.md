---
title: Azure Data Explorer API の概要 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の Azure Data Explorer API の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: c791ae0be0c895a4744e761c58e7d455aa0a22b9
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610274"
---
# <a name="azure-data-explorer-api-overview"></a>Azure Data Explorer API の概要

Azure Data Explorer サービスは、次の通信エンドポイントをサポートしています。

1. [REST API](#rest-api) エンドポイント。これを使用して、Azure Data Explorer でデータのクエリと管理を行うことができます。
   このエンドポイントでは、クエリに対する [Kusto クエリ言語](../query/index.md)と[制御コマンド](../management/index.md)がサポートされます。
2. [MS-TDS](#ms-tds) エンドポイント。これは、Microsoft SQL Server 製品で使用される Microsoft の表形式データ ストリーム (TDS) プロトコルのサブセットを実装します。
   このエンドポイントは、主に、クエリのための SQL Server エンドポイントとの通信方法を認識している既存のツールに役立ちます。
3. [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) エンドポイント。これは、Azure サービスで Azure Data Explorer クラスターなどのリソースを管理するための標準的な手段です。

Azure Data Explorer には、上記のエンドポイントを使用してプログラムによるアクセスを簡単にするクライアント ライブラリが多数用意されています。

1. .NET SDK
2. Python SDK
3. Java SDK
4. Node SDK
5. Go SDK
6. PowerShell
7. R

## <a name="rest-api"></a>REST API

Azure Data Explorer サービスと通信する主な手段は、サービスの REST API を使用することです。 この完全に文書化されたエンドポイントにより、呼び出し元は次のことが可能です。

1. クエリ データ
2. メタデータのクエリと変更
3. データの取り込み
4. サービスの正常性状態のクエリの実行
5. リソースの管理

異なる Azure Data Explorer サービスが、一般に提供されている同じ REST API を使って相互に通信しています。

REST API を使った Azure Data Explorer への要求のサポートに加え、REST API プロトコルを処理せずに、サービスを使用するために使用できる多数のクライアント ライブラリが用意されています。

## <a name="ms-tds"></a>MS-TDS

Azure Data Explorer に接続してそのデータのクエリを実行する別の方法として、Azure Data Explorer では Microsoft SQL Server 通信プロトコル (MS-TDS) がサポートされ、T-SQL クエリを実行するための制限付きサポートが組み込まれています。 これにより、ユーザーは、よく知られているクエリ構文 (T-SQL) と使い慣れたデータベース クライアント ツール (LINQPad、sqlcmd、Tableau、Excel、Power BI など) を使用して、Azure Data Explorer でクエリを実行できます。

MS-TDS の詳細については、[このページ](tds/index.md)を参照してください。

## <a name="net-framework-libraries"></a>.NET Framework ライブラリ

.NET Framework ライブラリは、Azure Data Explorer の機能をプログラムで呼び出すための推奨方法です。
多数の異なるライブラリが用意されています。

- [**Kusto.Data (Kusto クライアント ライブラリ)** ](./netfx/about-kusto-data.md)。これを使用して、データのクエリ、メタデータのクエリ、および変更を実行できます。
- [**Kusto.Ingest (Kusto インジェスト ライブラリ)** ](netfx/about-kusto-ingest.md)。これは、Kusto.Data を使用し、それを拡張してデータ インジェストを容易にします。


**Kusto クライアント ライブラリ** (Kusto.Data) は、Kusto REST API の上に構築され、ターゲットの Kusto クラスターに HTTPS 要求を送信します。 

**Kusto インジェスト ライブラリ** (Kusto.Ingest) では、Kusto.Data が使用されます。



上記のすべてのライブラリでは、Azure API (Azure Storage API、Azure Active Directory API など) が使用されます。

## <a name="python-libraries"></a>Python ライブラリ

Azure Data Explorer には、呼び出し元がデータ クエリおよび制御コマンドを送信できるようにする Python クライアント ライブラリが用意されています。

## <a name="r-library"></a>R ライブラリ

Azure Data Explorer には、呼び出し元がデータ クエリおよび制御コマンドを送信できるようにする R クライアント ライブラリが用意されています。



## <a name="using-azure-data-explorer-from-powershell"></a>PowerShell からの Azure Data Explorer の使用

Azure Data Explorer .NET Framework ライブラリは、PowerShell スクリプトによって使用できます。
例については、[PowerShell からの Azure Data Explorer の呼び出し](powershell/powershell.md)に関する記事を参照してください。

## <a name="ide-integration"></a>IDE 統合

`monaco-kusto` パッケージは、Microsoft によって開発された Web エディターであり、Visual Studio Code の基礎となる Monaco Editor との統合をサポートしています。
monaco-kusto パッケージの詳細については、[こちら](monaco/monaco-kusto.md)をご覧ください。