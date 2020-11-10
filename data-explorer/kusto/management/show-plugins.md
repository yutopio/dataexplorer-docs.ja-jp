---
title: プラグインコマンドプラグインの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのプラグイン管理コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1db761d8c290c7aaea452cd0cb3d85f2f648221c
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422118"
---
# <a name="show-plugins"></a>。プラグインを表示します


クラスターのすべてのプラグインを一覧表示します。

## <a name="syntax"></a>構文

`.show` `plugins`

## <a name="output"></a>出力

次のフィールドを含むテーブルを返します。
* **Pluginname** : プラグインの名前
* **IsEnabled** : プラグインが有効かどうかを示すブール値です。

## <a name="example"></a>例

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | true  |

## <a name="next-steps"></a>次のステップ

* [。プラグインを無効にします](disable-plugin.md)
* [。プラグインを有効にします。](enable-plugin.md)
