---
title: プラグインコマンドを有効にする-Azure データエクスプローラー
description: この記事では、プラグイン管理コマンドについて説明します。 Azure データエクスプローラーでプラグインを有効にします。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: a4da3848fa459cf5fae8a7a73f8b1f318ce7e858
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321473"
---
# <a name="enable-plugin"></a>.enable plugin

プラグインを有効にします。

このコマンドには `All Databases admin` アクセス許可が必要です。

## <a name="syntax"></a>構文

`.enable``plugin` *Pluginname*

## <a name="example"></a>例

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>次のステップ

* [`.disable plugin`](disable-plugin.md)
* [`.show plugins`](show-plugins.md)

