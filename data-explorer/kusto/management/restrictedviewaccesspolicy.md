---
title: Kusto RestrictedViewAccess ポリシー制御クエリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの RestrictedViewAccess ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: be9eea23373489faf44c70146109fe1488877269
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967555"
---
# <a name="restricted-view-access-policy"></a>制限付きビュー アクセス ポリシー

*RestrictedViewAccess*は、データベースのテーブルに対して有効にできるオプションのポリシーです。

テーブルに対してこのポリシーが有効になっている場合、テーブル内のデータは、データベースに[UnrestrictedViewer](../management/access-control/role-based-authorization.md)ロールを持つプリンシパルだけがクエリを実行できます。
[UnrestrictedViewer](../management/access-control/role-based-authorization.md)データベースレベルのロールに登録されていないプリンシパルは、テーブル内のデータに対してクエリを実行できません。 未登録のテーブル/データベース/クラスター管理者でもあります。

[UnrestrictedViewer](../management/access-control/role-based-authorization.md)ロールは、ポリシーが有効になっているデータベース内の*すべて*のテーブルに対して view 権限を付与します。
現在のプリンシパル (データベース管理者/ユーザー/ビューアー) は、既にデータベースのクエリを実行する権限を持っています。 プリンシパルの追加または削除は、 [Databaseadmin](../management/access-control/role-based-authorization.md)で行うことができます。

> [!NOTE]
> [行レベルセキュリティポリシー](./rowlevelsecuritypolicy.md)が有効になっているテーブルでは、RestrictedViewAccess ポリシーを構成できません。

詳細については、「 [RestrictedViewAccess ポリシー](../management/restrictedviewaccess-policy.md)を管理するための制御コマンド」を参照してください。
