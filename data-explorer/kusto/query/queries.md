---
title: クエリ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのクエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2ac338600320d3f83a92e22e405f630ee308df2f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510780"
---
# <a name="queries"></a>クエリ

クエリは、Kusto Engine クラスターの取り込みデータに対する読み取り専用操作です。 クエリは常にクラスタ内の特定のデータベースのコンテキストで実行されます (ただし、別のデータベースのデータや別のクラスタのデータを参照する場合もあります)。

データのアドホック クエリは Kusto の最優先シナリオであるため、Kusto クエリ言語構文は、データに対してクエリを作成および実行する専門家以外のユーザーに最適化され、各クエリの実行内容を明確に理解できます (論理的に)。

言語の構文はデータ フローの構文であり、"データ" は実際には "表形式のデータ" (1 つ以上の行/列の四角形のデータ) を意味します。 少なくとも、クエリはソース データ参照 (Kusto テーブルへの参照) と、パイプ文字 (`|`) を使用して演算子を区切ることによって、順番に適用される 1 つ以上の**クエリ演算子**で構成されます。

次に例を示します。

```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```
    
パイプ文字`|`が先頭に配置された各フィルターは、いくつかのパラメーターが設定される*演算子*のインスタンスです。 この演算子への入力は、前のパイプラインの結果であるテーブルです。 ほとんどの場合、パラメーターは入力の列に対する[スカラー式](./scalar-data-types/index.md)ですが、
まれに、入力列の名前である場合や、2 つ目のテーブルである場合もあります。 列と行が 1 つずつしかなくても、クエリの結果は常にテーブルです。

## <a name="reference-query-operators"></a>リファレンス: クエリ演算子

> `T` が使用されています。