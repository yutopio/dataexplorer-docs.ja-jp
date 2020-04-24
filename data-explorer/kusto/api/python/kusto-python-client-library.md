---
title: Azure データ エクスプローラー Python SDK - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Azure データ エクスプローラー の Python SDK について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5bee1fce1ca76069c34602872b2d4dedeb762ec2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502858"
---
# <a name="azure-data-explorer-python-sdk"></a>Azure データ エクスプ ローラーの Python SDK

Azure データ エクスプローラー *Kusto Python クライアント*ライブラリには、Python を使用して Azure データ エクスプローラー クラスターを照会する機能が用意されています。 Python 2.x/3.x 互換で、使い慣れた Python DB API インターフェイスを使用してすべてのデータ型をサポートしています。

たとえば、Spark クラスターに接続されている[Jupyter ノートブック](https://jupyter.org/) [(Azure Databricks](https://azure.microsoft.com/services/databricks/)インスタンスを含むが排他的ではない) からライブラリを使用できます。

*Kusto Python インジェスト クライアント*は、データを Azure データ エクスプローラ サービス (つまり取り込みデータ) に送信できる Python ライブラリです。 

* [パッケージをインストールする方法](https://github.com/Azure/azure-kusto-python#install)

* [Kusto クエリのサンプル](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [データ取り込みサンプル](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [GitHub リポジトリ](https://github.com/Azure/azure-kusto-python)

    [![alt text](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* ピピパッケージ:

    * [azure-kusto-data](https://pypi.org/project/azure-kusto-data/)
    [![PyPI バージョン](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [PyPI バージョンを摂取する](https://pypi.org/project/azure-kusto-ingest/)
    [![](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [azure-mgmt-kusto](https://pypi.org/project/azure-mgmt-kusto/)
    [![PyPI バージョン](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)