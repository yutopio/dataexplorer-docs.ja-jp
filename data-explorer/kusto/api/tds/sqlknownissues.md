---
title: SQL Server との kusto MS-TDS/T-sql の相違点-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの Kusto Microsoft SQL Server 間の MS TDS/T-sql の違いについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: c949b5bb3659d82ed586a39b4310495e61934777
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382371"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>Kusto Microsoft SQL Server 間の MS-TDS/T-sql の相違点

次に示すのは、Kusto と SQL Server による T-sql の実装の主な違いを示しています。

## <a name="create-insert-drop-alter-statements"></a>CREATE、INSERT、DROP、ALTER ステートメント

Kusto は、MS TDS を使用したスキーマの変更やデータの変更をサポートしていません。また、上記の T-sql ステートメントをサポートしていません。

## <a name="correlated-sub-queries"></a>相関サブクエリ

Kusto `SELECT` では、、、および句での相関サブクエリはサポートされていません `WHERE` `JOIN` 。

## <a name="top-flavors"></a>トップフレーバー

Kusto は `WITH TIES` 、クエリを無視し、標準として評価し `TOP` ます。
Kusto はサポートしていません `PERCENT` 。

## <a name="cursors"></a>カーソル

Kusto では、SQL カーソルはサポートされていません。

## <a name="flow-control"></a>フロー制御

Kusto では、フロー制御ステートメントはサポートされません。ただし、 `IF` `THEN` `ELSE` とのバッチに同一のスキーマを持つ句など、少数のケースがあり `THEN` `ELSE` ます。

## <a name="data-types"></a>データ型

クエリによっては、返されるデータの型が SQL Server とは異なる場合があります。
ここでのわかりやすい例として `TINYINT` 、Kusto に相当するやなどの型があり `SMALLINT` ます。 したがって、型または型の値を予期しているクライアントは、 `BYTE` `INT16` 値または値を取得することがあり `INT32` `INT64` ます。

## <a name="column-order-in-results"></a>結果内の列の順序

ステートメントでアスタリスクを使用する場合 `SELECT` 、各結果セットの列の順序が kusto と SQL Server で異なる場合があります。 このような場合、列名を使用するクライアントの方が適しています。
ステートメントにアスタリスク文字がない場合は `SELECT` 、列の序数が保持されます。

## <a name="columns-name-in-results"></a>結果内の列名

T-sql では、複数の列が同じ名前を持つ場合があります。 これは Kusto では許可されていません。
名前の競合が発生した場合、列の名前が Kusto と異なる場合があります。
ただし、少なくともいずれかの列に対して、元の名前が保持されます。

## <a name="any-all-and-exists-predicates"></a>ANY、ALL、および EXISTS 述語

Kusto は、述語、、およびをサポートしていません `ANY` `ALL` `EXISTS` 。

## <a name="recursive-ctes"></a>再帰 CTE

Kusto では、再帰共通テーブル式はサポートされていません。

## <a name="dynamic-sql"></a>動的 SQL

Kusto では、動的 SQL ステートメント (クエリによって生成される SQL スクリプトのインライン実行) はサポートされません。

## <a name="within-group"></a>WITHIN GROUP

Kusto は句をサポートしていません `WITHIN GROUP` 。

## <a name="truncate-function"></a>TRUNCATE 関数

Kusto の TRUNCATE 関数 (ODBC) は、ROUND と同様に動作します。これは、結果が SQL で返される下位の値ではなく、最も近い値になることを意味します。