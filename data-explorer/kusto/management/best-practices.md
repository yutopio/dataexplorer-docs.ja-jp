---
title: スキーマ設計のベスト プラクティス - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのスキーマ設計のベスト プラクティスについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f9e2d4a2ad50b140d041179736b48a41a424f5c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522170"
---
# <a name="best-practices-for-schema-design"></a>スキーマ設計のベスト プラクティス

管理コマンドのパフォーマンスを向上させ、サービスに対する影響を軽くするために、いくつかの「Dos and Don'ts」を使用できます。

## <a name="do"></a>次のことを行います

1. 複数のテーブルを作成する必要がある場合[`.create tables`](create-tables-command.md)は、コマンドを多数`.create table`発行する代わりに、このコマンドを使用します。
2. 複数のテーブルの名前を変更する必要がある場合は、テーブルの[`.rename tables`](rename-table-command.md)ペアごとに個別の呼び出しを発行するのではなく、 に対して 1 回の呼び出しを行います。
3. パイプの後にフィルタを`.show`適用するのではなく、最もスコープの低いコマンドを`|`使用します( ) 次に例を示します。
    - `.show cluster extents | where TableName == 'T'` の代わりの `.show table T extents` の使用
    - `.show database DB schema` の代わりに `.show schema | where DatabaseName == 'DB'` を使用します。
4. 1`.show table T`つのテーブルで実際の統計情報を取得する必要がある場合にのみ使用します。 テーブルの存在を確認するだけ、またはテーブルのスキーマを取得するだけの場合は、 を`.show table T schema as json`使用します。
5. 日時値を含むテーブルのスキーマを定義する場合は、これらの列が`datetime`型で型指定されていることを確認してください。
    - Kusto は、列のフィルター処理用に`datetime`高度に最適化されています。 フィルター処理の際`string`に、入力時間の前または`long`処理中に行`datetime`うことができる場合は、列をクエリ時に変換したり数値 (例) にしたりしないでください。

## <a name="dont"></a>次のことは行わないでください

1. コマンドを頻繁`.show`に実行しないでください`.show schema``.show databases``.show tables`( など ) 。 可能な場合 - 返される情報をキャッシュします。
2. 大規模なスキーマ`.show schema`(100 個を超えるデータベースを含む) クラスターでコマンドを実行しないでください。 代わりに、[`.show databases schema`](../management/show-schema-database.md)を使用します。
3. [コマンドを実行し、クエリ操作を](index.md#combining-queries-and-control-commands)頻繁に実行しないでください。
    - *コマンドの次のクエリ*: 制御コマンドの結果セットをパイピングし、そのコマンドにフィルター/集計を適用することを意味します。
        - 例: `.show ... | where ... | summarize ...`
    - 次のような動作`| count`を実行`.show cluster extents | count`する場合: (を強調して) Kusto は、まずクラスタ内のすべてのエクステントのすべての詳細を保持するデータ テーブルを準備し、そのメモリ内専用テーブルを Kusto エンジンに送信してカウントを実行します。 これは、Kustoが実際に最適化されていないパスで非常に懸命に働いて、あなたにそのような些細な答えを返すことを意味します。
4. データ取り込みの一部としてエクステント タグを過度に使用する。 特にタグ`drop-by:`を使用する場合は、システムがパフォーマンス指向のグルーミングプロセスをバックグラウンドで実行する機能を制限します。
    - パフォーマンスに関[する注意事項はこちらをご覧ください](../management/extents-overview.md#extent-tagging)。
    