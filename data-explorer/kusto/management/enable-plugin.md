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
ms.openlocfilehash: a44ebec6774374f4d38dfda3babe42f2f5e07ac6
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422102"
---
# <a name="enable-plugin"></a>。プラグインを有効にします。

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

* [。プラグインを無効にします](disable-plugin.md)
* [。プラグインを表示します](show-plugins.md)

