---
title: current_principal_details ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの current_principal_details () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: cf10fcfd66b7a9240746a904e4979c8091c3518b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348685"
---
# <a name="current_principal_details"></a>current_principal_details()

クエリを実行しているプリンシパルの詳細を返します。

## <a name="syntax"></a>構文

`current_principal_details()`

## <a name="returns"></a>戻り値

としての現在のプリンシパルの詳細 `dynamic` 。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName": " user@fabrikam.com ",<br>  "IdentityProvider": " https://sts.windows.net ",<br>  "Authority": "72f988bf-86f1-41af-91ab-2d7cd011db47",<br>  "Mfa": "True"、<br>  "Type": "AadUser",<br>  "DisplayName": "James Smith (upn: user@fabrikam.com )",<br>  "ObjectId": "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "次の値ではない": null,<br>  "Notes": null<br>}|
