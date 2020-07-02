---
title: Azure データエクスプローラー Python SDK-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Python SDK について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 7718eb5983f1d7893a27df5db7ae0c52983dea85
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763843"
---
# <a name="azure-data-explorer-python-sdk"></a>Azure データエクスプローラー Python SDK

Azure データエクスプローラー *Kusto Python クライアント*ライブラリを使用すると、python を使用して azure データエクスプローラークラスターに対してクエリを実行できます。 ライブラリは Python 2.x/2.x と互換性があります。 Python DB API インターフェイスを使用して、すべてのデータ型をサポートします。

ライブラリを使用できます。たとえば、Spark クラスターに接続されている[Jupyter notebook](https://jupyter.org/)から、 [Azure Databricks](https://azure.microsoft.com/services/databricks/)インスタンスだけでなく、このライブラリを使用することもできます。

*Kusto Python インジェストクライアント*は、Azure データエクスプローラーサービスにインジェストデータを送信できる python ライブラリです。

## <a name="next-steps"></a>次のステップ

* [パッケージをインストールする方法](https://github.com/Azure/azure-kusto-python#install)

* [Kusto クエリのサンプル](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [データインジェストのサンプル](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [GitHub リポジトリ](https://github.com/Azure/azure-kusto-python)

    [![alt text](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* Pypi パッケージ:

    * [azure-kusto データ](https://pypi.org/project/azure-kusto-data/) 
    [ ![ PyPI のバージョン](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [azure-kusto インジェスト](https://pypi.org/project/azure-kusto-ingest/) 
    [ ![ PyPI のバージョン](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [azure-管理-kusto](https://pypi.org/project/azure-mgmt-kusto/) 
    [ ![ PyPI のバージョン](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)
