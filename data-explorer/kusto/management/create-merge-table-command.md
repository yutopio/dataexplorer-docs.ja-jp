---
title: . create-merge table-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの create-merge テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 554f6ed623b5a3be12a360fab0b1d5aa6eb4c084
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320963"
---
# <a name="create-merge-table"></a>.create-merge table

新しいテーブルを作成するか、既存のテーブルを拡張します。 

コマンドは、特定のデータベースのコンテキストで実行する必要があります。 

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

## <a name="syntax"></a>構文

`.create-merge``table` *TableName* ([columnName: columnType],...) [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ]

テーブルが存在しない場合、はコマンドとまったく同じように機能し `.create table` ます。

テーブル T が存在し、コマンドを送信する場合は `.create-merge table T (<columns specification>)` 、次のようになります。

* <columns specification>以前に t に存在していなかったの列は、t のスキーマの末尾に追加されます。
* に含まれていない T 内の列は、 <columns specification> t から削除されません。
* の列が <columns specification> T に存在するが、データ型が異なる場合、コマンドは失敗します。

## <a name="see-also"></a>関連項目

* [`.create-merge tables`](create-merge-tables-command.md)
* [`.create table`](create-table-command.md)
