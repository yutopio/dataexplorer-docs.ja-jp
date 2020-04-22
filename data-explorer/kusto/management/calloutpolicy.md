---
title: コールアウト ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのコールアウト ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: df57c4901cef74d574108d2c6e75672d1faba75c
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744519"
---
# <a name="callout-policy"></a>Callout ポリシー

## <a name="overview"></a>概要

Azure データ エクスプローラー クラスターは、さまざまなシナリオで外部サービスと通信できます。
クラスター管理者は、クラスターのコールアウト ポリシーを更新することで、外部呼び出しに対して許可されたドメインを管理できます。

コールアウト ポリシーはクラスタ レベルで管理されており、次のタイプに分類されます。
* `kusto`- Azure データ エクスプローラーのクラスター間クエリを制御します。
* `sql`- [SQL プラグイン](../query/sqlrequestplugin.md)を制御します。


* `webapi`- 他の外部 Web 呼び出しを制御します。
* `sandbox_artifacts`- サンドボックスプラグイン[(Python](../query/pythonplugin.md) | [R)](../query/rplugin.md)を制御します。

コールアウト ポリシーは、次のコンポーネントで構成されます。
* **CalloutType は**吹き出しの種類を定義し、次のいずれかになります。
* **コールアウト**のドメインで許可されている正規表現を指定します。
* **CanCall**は、コールアウトが外部呼び出しを許可するかどうかを示します。

## <a name="predefined-callout-policies"></a>定義済みのコールアウト ポリシー

すべての Azure Data Explorer クラスターで、コールアウトを簡単に選択できるように、定義済みのコールアウト ポリシーのセットが、すべての Azure Data Explorer クラスターで不変に構成されています。

|サービス      |クラウド        |指定  |許可されたドメイン |
|-------------|-------------|-------------|-------------|
|Kusto |パブリック Azure |クロス クラスター クエリ |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |黒い森 |クロス クラスター クエリ |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |クロス クラスター クエリ |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |ムーンケーキ |クロス クラスター クエリ |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |パブリック Azure |SQL 要求 |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |黒い森 |SQL 要求 |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |SQL 要求 |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |ムーンケーキ |SQL 要求 |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|ベースラインサービス |パブリック Azure |要求のベースライン化 |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## <a name="control-commands"></a>管理コマンド

コマンドには[、すべてのデータベース管理者の](access-control/role-based-authorization.md)アクセス許可が必要です。

**構成済みのすべてのコールアウト ポリシーを表示**

```kusto
.show cluster policy callout
```

**コールアウト・ポリシーの終了を変更する**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**許可された吹き出しのセットを追加する**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**変更できないすべてのコールアウト ポリシーを削除する**

```kusto
.delete cluster policy callout
```
