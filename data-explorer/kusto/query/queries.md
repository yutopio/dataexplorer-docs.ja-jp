---
title: クエリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8be14a48f5b24d344454d9aa5012f48e7d7e2b8e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250960"
---
# <a name="query-operators"></a>クエリ演算子

クエリは、Kusto エンジンクラスターの取り込まれたデータに対する読み取り専用操作です。 クエリは、常にクラスター内の特定のデータベースのコンテキストで実行されます。 また、別のデータベースまたは別のクラスター内のデータを参照している場合もあります。

データのアドホッククエリは Kusto の最優先事項のシナリオであるため、Kusto クエリ言語の構文は、専門家ではないユーザーがデータに対してクエリを作成して実行し、各クエリの内容 (論理的) を明確に理解できるように最適化されています。

言語の構文はデータフローのものであり、"データ" は "表形式のデータ" (1 つ以上の行/列の四角形の形のデータ) を意味します。 少なくとも、クエリは、ソースデータ参照 (Kusto テーブルへの参照) と1つ以上の **クエリ演算子** (パイプ文字 () を使用して演算子を区切ることによって視覚的に示される) で構成され `|` ます。

次に例を示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```

パイプ文字`|`が先頭に配置された各フィルターは、いくつかのパラメーターが設定される*演算子*のインスタンスです。 この演算子への入力は、前のパイプラインの結果であるテーブルです。 ほとんどの場合、パラメーターは入力の列に対する[スカラー式](./scalar-data-types/index.md)ですが、
まれに、入力列の名前である場合や、2 つ目のテーブルである場合もあります。 列と行が 1 つずつしかなくても、クエリの結果は常にテーブルです。

`T` は、前のパイプラインまたはソーステーブルを示すためにクエリで使用されます。
