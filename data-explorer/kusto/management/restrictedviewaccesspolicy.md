---
title: 制限付きビューアクセス ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの制限されたビュー アクセス ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6f994f5b80632650ab6dbe5dcf28cd82407d688f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520419"
---
# <a name="restrictedviewaccess-policy"></a>ポリシーを制限

*制限付きビューアクセス*は、データベース内のテーブルで有効にできるオプションのポリシーです。

テーブルでこのポリシーを有効にすると、テーブル内のデータは、データベースの[UnrestrictedViewer](../management/access-control/role-based-authorization.md)ロールに追加されたプリンシパル*にのみ*照会できます。

テーブルでポリシーが有効になっている場合[、UnrestrictedViewer](../management/access-control/role-based-authorization.md)データベース レベル ロールに含まれていないプリンシパル (テーブル/データベース/クラスター管理者も含む) は、テーブル内のデータを照会できません。

[UnrestrictedViewer](../management/access-control/role-based-authorization.md)ロールは、現在のプリンシパルがデータベースのクエリを既に承認されている場合 (データベース管理者/ユーザー/ビューアー) を使用して、ポリシーが有効になっているデータベース*内のすべての*テーブルに対してビュー権限を付与します。 ロールに対するプリンシパルの追加またはロールからのプリンシパルの削除は、 [DatabaseAdmin](../management/access-control/role-based-authorization.md)によって実行できます。

> [!NOTE]
> 行レベルセキュリティ ポリシーが有効になっているテーブルに対して、制限付き[ViewAccessポリシー](./rowlevelsecuritypolicy.md)を設定することはできません。

ポリシーを管理するための制御コマンドの詳細については、 ここ を[参照してください](../management/restrictedviewaccess-policy.md)。