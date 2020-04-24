---
title: テイクオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの使用するオペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c8f724e139e13bf9ece00d5af09f2a3cf25b03a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506598"
---
# <a name="take-operator"></a>take 演算子

指定した行数までを返します。

```kusto
T | take 5
```

ソース データが並べ替えられない限り、どのレコードが返されるかは保証されません。

**構文**

`take`*行数*
`limit`*の数*

(`take`と`limit`は同義語です。

**メモ**

`take`データを対話的に参照するときに、レコードの小さなサンプルを表示するシンプルで迅速かつ効率的な方法ですが、データ セットが変更されていない場合でも、複数回実行しても結果の一貫性は保証されません。

クエリによって返される行数がクエリによって明示的に制限されていない場合でも (演算子は使用`take`されません)、Kusto はデフォルトでその数を制限します。
詳細については[、Kusto クエリの制限](../concepts/querylimits.md)を参照してください。

参照項目:[ソート演算子](sortoperator.md)
[のトップネスト演算子](topoperator.md)
[top-nested operator](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto はクエリ結果のページングをサポートしていますか?

Kusto は組み込みのページングメカニズムを提供しません。

Kusto は、格納するデータを継続的に最適化して、膨大なデータ セットに対して優れたクエリ パフォーマンスを提供する複雑なサービスです。 ページングは、リソースが限られているステートレス クライアントにとって便利なメカニズムですが、クライアントの状態情報を追跡する必要があるバックエンド サービスに負担を移します。 その後、バックエンド サービスのパフォーマンスとスケーラビリティは大幅に制限されます。

ページングのサポートでは、次の機能のいずれかを実装します。

* クエリの結果を外部ストレージにエクスポートし、生成されたデータをページングする。

* Kusto クエリの結果をキャッシュすることによって、ステートフル ページング API を提供する中間層アプリケーションを作成する。