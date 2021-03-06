---
title: スキーマ管理のベストプラクティス-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのスキーマ管理のベストプラクティスについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: ba0d931fcca257cb3b5658354dfb8bf70dc6848f
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774624"
---
# <a name="best-practices-for-schema-management"></a>スキーマ管理のベストプラクティス

次に、いくつかのベストプラクティスを示します。 管理コマンドの動作が向上し、サービスリソースに対する影響が大きくなります。

|アクション  |vmmblue_2  |使用しない | メモ |
|---------|---------|---------|----
| **複数のテーブルを作成する**    |  1つのコマンドを使用する [`.create tables`](create-tables-command.md)       | 多くのコマンドを発行しない `.create table`        | |
| **複数のテーブル名の変更**    | を1回呼び出します。 [`.rename tables`](rename-table-command.md)        |  テーブルのペアごとに個別の呼び出しを実行しない   |    |
|**コマンドの表示**   |   最も低いスコープのコマンドを使用する `.show` |   パイプ () の後にフィルターを適用しない `|`   </ul></li>  | 制限は可能な限り使用してください。 可能であれば、返された情報をキャッシュします。 |
| エクステントの表示  | `.show table T extents` を使用します   |使用しない `.show cluster extents | where TableName == 'T'`  |
|  データベーススキーマを表示します。 |`.show database DB schema` を使用します  |  使用しない `.show schema | where DatabaseName == 'DB'` |
| **大きなスキーマを持つクラスターにスキーマを表示する** <br> |使用 [`.show databases schema`](../management/show-schema-database.md) |使用しない `.show schema`| たとえば、100以上のデータベースを持つクラスターでを使用します。
| **テーブルの存在を確認するか、テーブルのスキーマを取得します。**|`.show table T schema as json` を使用します|使用しない  `.show table T` |このコマンドは、1つのテーブルで実際の統計情報を取得する場合にのみ使用します。|
| **値を含むテーブルのスキーマを定義します。 `datetime`**  |関連する列を型に設定します。 `datetime` | `string`フィルター処理のためにクエリ時にまたは数値列をに変換しないでください。 `datetime` 取り込み時間の前または後に行うことができます。|
| **エクステントタグをメタデータに追加** |控えめに使用する |`drop-by:`バックグラウンドでパフォーマンス指向のクリーンアッププロセスを実行するシステムの能力を制限する、タグを避けます。|  <br> 「 [パフォーマンスのメモ](../management/extents-overview.md#extent-tagging)」を参照してください。 |
