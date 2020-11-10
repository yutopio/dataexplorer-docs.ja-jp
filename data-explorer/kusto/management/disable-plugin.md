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
ms.openlocfilehash: 1dd2649d4746506f0ef2c08d4615260babd594e1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422115"
---
# <a name="disable-plugin"></a>。プラグインを無効にします

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

* [。プラグインを表示します](show-plugins.md)
* [。プラグインを有効にします。](enable-plugin.md)

