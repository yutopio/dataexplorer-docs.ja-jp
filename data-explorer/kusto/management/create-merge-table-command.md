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
ms.openlocfilehash: afe5011717fd77d654eaf6c2b70e9ffbdea87128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967631"
---
# <a name="create-merge-table"></a>. create-merge テーブル

新しいテーブルを作成するか、既存のテーブルを拡張します。 

コマンドは、特定のデータベースのコンテキストで実行する必要があります。 

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create-merge``table` *TableName* ([columnName: columnType],...) [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ]

テーブルが存在しない場合、は ". create table" コマンドとまったく同じように機能します。

テーブル T が存在し、". create-merge table T ()" コマンドを送信する場合は、次の <columns specification> ようになります。

* <columns specification>以前に t に存在していなかったの列は、t のスキーマの末尾に追加されます。
* に含まれていない T 内の列は、 <columns specification> t から削除されません。
* の列が <columns specification> T に存在するが、データ型が異なる場合、コマンドは失敗します。

**参照**

* [.create-merge tables](create-merge-tables-command.md)
* [.create テーブル](create-table-command.md)
