---
title: 部分的なクエリエラー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの部分的なクエリエラーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d65256c0ac50a80e3e3b095e8d2348dbae5e585b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523122"
---
# <a name="partial-query-failures"></a>部分的なクエリ エラー

*部分クエリの失敗*は、クエリが実際の実行フェーズを開始した後にのみ検出されるクエリの実行エラーです。 その時点で、Kusto はすでに HTTP ステータス`200 OK`ラインをクライアントに返し、"取り戻す" ことができないため、クエリ結果をクライアントに返す結果ストリームの失敗を示す必要があります。 (実際には、呼び出し元に返される結果データが既にある可能性があります)。

部分クエリエラーには、いくつかの種類があります。
* [ランナウェイ クエリ](runawayqueries.md): リソースを多く取り込むクエリ。
* [結果の切り捨て](resulttruncation.md): 制限を超えた結果セットが切り捨てられたクエリ。
* [オーバーフロー](overflow.md): オーバーフロー エラーをトリガーするクエリ。

部分的なクエリエラーは、次の 2 つの方法のいずれかでクライアントに報告できます。

* 結果データの一部として (特殊な種類のレコードは、これがデータそのものではなくなっていることを示します)。 これがデフォルトの方法です。
* 結果ストリームの "QueryStatus" テーブルの一部として。 これは、要求の`deferpartialqueryfailures``properties`スロット ( )`Kusto.Data.Common.ClientRequestProperties.OptionDeferPartialQueryFailures`のオプションを使用して行われます。
  サービスからの結果ストリーム全体を使用し、`QueryStatus`結果を見つけ、この結果にレコードが 以下`Severity``2`を含まないようにする責任を負うクライアント。 