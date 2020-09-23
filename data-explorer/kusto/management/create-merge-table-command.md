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
ms.openlocfilehash: 19dc7db9e344a516b5c92917dccbf8362b1ca858
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102888"
---
# <a name="create-merge-table"></a>.create-merge table

新しいテーブルを作成するか、既存のテーブルを拡張します。 

コマンドは、特定のデータベースのコンテキストで実行する必要があります。 

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

## <a name="syntax"></a>構文

`.create-merge``table` *TableName* ([columnName: columnType],...) [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ]

テーブルが存在しない場合、は ". create table" コマンドとまったく同じように機能します。

テーブル T が存在し、". create-merge table T ()" コマンドを送信する場合は、次の <columns specification> ようになります。

* <columns specification>以前に t に存在していなかったの列は、t のスキーマの末尾に追加されます。
* に含まれていない T 内の列は、 <columns specification> t から削除されません。
* の列が <columns specification> T に存在するが、データ型が異なる場合、コマンドは失敗します。

## <a name="see-also"></a>関連項目

* [.create-merge tables](create-merge-tables-command.md)
* [。テーブルを作成します。](create-table-command.md)
