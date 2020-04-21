---
title: クストーマイクロソフトSQLサーバー間のMS-TDS/T-SQLの違い - Azureデータエクスプローラ |マイクロソフトドキュメント
description: この資料では、Azure データ エクスプローラーでのクススト マイクロソフト SQL サーバー間の MS-TDS/T-SQL の違いについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: be294053fdd0f95d488f52b547ef7abd0ef7c23c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523428"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>クストーマイクロソフトSQLサーバー間のMS-TDS/T-SQLの違い

以下は、Kusto と SQL Server の T-SQL の実装の主な違いの一部です。

## <a name="create-insert-drop-alter-statements"></a>作成、挿入、削除、およびステートメントの変更

KUsto は、MS-TDS を使用したスキーマの変更やデータ変更をサポートしません。

## <a name="correlated-sub-queries"></a>相関サブクエリ

Kusto は、 、`SELECT``WHERE`および 句の相関サブクエリを`JOIN`サポートしていません。

## <a name="top-flavors"></a>トップフレーバー

Kusto は`WITH TIES`クエリを無視して、通常`TOP`の クエリとして評価します。
クストはサポート`PERCENT`していません.

## <a name="cursors"></a>カーソル

KUSTO は SQL カーソルをサポートしていません。

## <a name="flow-control"></a>フロー制御

Kusto は、および`IF``THEN``ELSE``ELSE`バッチに同じスキーマを持つ句など、いくつかの限定されたケースを除き、フロー`THEN`制御ステートメントをサポートしていません。

## <a name="data-types"></a>データ型

クエリによっては、返されるデータの種類が SQL Server と異なる場合があります。
ここでの明白な例は`TINYINT`、Kusto`SMALLINT`に相当するものがないような型です。 したがって、`BYTE`型の値を期待するクライアント、`INT16`または代わりに`INT32`値`INT64`を取得するクライアント。

## <a name="column-order-in-results"></a>結果の列の順序

ステートメントで asterix を`SELECT`使用する場合、各結果セットの列の順序は Kusto と SQL Server の間で異なる場合があります。 このような場合、列名を使用するクライアントの方が適切です。
`SELECT`ステートメントに asterix 文字がない場合は、列序数が保持されます。

## <a name="columns-name-in-results"></a>結果の列名

T-SQL では、複数の列に同じ名前を付ける場合があります。 これは、クストーでは許可されていません。
名前が衝突した場合、Kusto では列の名前が異なる場合があります。
ただし、少なくとも 1 つの列では、元の名前が保持されます。

## <a name="any-all-and-exists-predicates"></a>任意、すべて、および存在の述部

Kusto は述語`ANY`、、`ALL`および`EXISTS`をサポートしていません。

## <a name="recursive-ctes"></a>再帰 CTE

Kusto は再帰的共通テーブル式をサポートしていません。

## <a name="dynamic-sql"></a>動的 SQL

Kusto は動的 SQL ステートメントをサポートしていません (クエリによって生成された SQL スクリプトのインライン実行)。

## <a name="within-group"></a>WITHIN GROUP

クストは条項をサポート`WITHIN GROUP`していません。

## <a name="truncate-function"></a>切り捨て関数

クストの TRUNCATE 関数 (ODBC) は ROUND と同様に動作するため、結果は SQL で返される下位の値ではなく、最も近い値になります。