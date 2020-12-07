---
title: プラグインコマンドを無効にする-Azure データエクスプローラー
description: この記事では、プラグイン管理コマンドについて説明します。 Azure データエクスプローラーでプラグインを無効にします。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1063355f880f26a321eb6416180ae9764575f0be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320895"
---
# <a name="disable-plugin"></a>.disable plugin

プラグインを無効にします。

このコマンドには `All Databases admin` アクセス許可が必要です。

## <a name="syntax"></a>構文

`.disable``plugin` *Pluginname*

## <a name="example"></a>例
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>次のステップ

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

