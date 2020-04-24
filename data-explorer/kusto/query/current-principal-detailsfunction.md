---
title: current_principal_details() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで current_principal_details() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 5418647c811b034bb5790dfff3fd17f500c52db0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516781"
---
# <a name="current_principal_details"></a>current_principal_details()

クエリを実行しているプリンシパルの詳細を返します。

**構文**

`current_principal_details()`

**戻り値**

現在のプリンシパルの詳細です`dynamic`。

**例**

```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "ユーザープリンシパル名" :user@fabrikam.com""<br>  「アイデンティティプロバイダー」:https://sts.windows.net""<br>  「権限」: "72f988bf-86f1-41af-91ab-2d7cd011db47",<br>  「Mfa」:"真"、<br>  「タイプ」:"AadUser"、<br>  「表示名」:"ジェームズ・スミス(アップン:)"、 user@fabrikam.com<br>  "ObjectId": "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "FQN": null、<br>  "メモ": null<br>}|
