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
ms.openlocfilehash: e44aa2b14aa8babdab95a6ad8c6f7ef5ed026ff9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617428"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess ポリシー

*RestrictedViewAccess*は、データベース内のテーブルで有効にできるオプションのポリシーです。

テーブルでこのポリシーが有効になっている場合、テーブル内のデータは、データベースの[UnrestrictedViewer](../management/access-control/role-based-authorization.md)ロールに追加されたプリンシパルに対して*のみ*クエリを実行できます。

テーブルでポリシーが有効になっている場合、 [UnrestrictedViewer](../management/access-control/role-based-authorization.md)データベースレベルのロールに含まれていないプリンシパル (テーブル/データベース/クラスター管理者であっても) は、テーブル内のデータを照会することはできません。

[UnrestrictedViewer](../management/access-control/role-based-authorization.md)ロールは、現在のプリンシパルがデータベース (データベース管理者/ユーザー/ビューアー) に対してクエリを実行する権限を既に持っていると仮定して、ポリシーが有効になっているデータベース内の*すべて*のテーブルに view 権限を付与します。 ロールに対してプリンシパルの追加や削除を行うには、 [Databaseadmin](../management/access-control/role-based-authorization.md)を使用します。

> [!NOTE]
> [行レベルセキュリティポリシー](./rowlevelsecuritypolicy.md)が有効になっているテーブルでは、RestrictedViewAccess ポリシーを構成できません。

RestrictedViewAccess ポリシーを管理するための制御コマンドの詳細については、[こちらを参照してください](../management/restrictedviewaccess-policy.md)。