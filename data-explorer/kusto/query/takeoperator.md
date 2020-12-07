---
title: take operator-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの take 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 569554379ffe672ed75fd15da127f03fea35f6d1
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748384"
---
# <a name="take-operator"></a>take 演算子

指定された行数まで返します。

```kusto
T | take 5
```

ソースデータを並べ替える場合を除き、どのレコードが返されるかは保証されません。

> [!NOTE]
> `take` は、データを対話的に参照しているときに小さなサンプルレコードを表示するためのシンプルで迅速かつ効率的な方法ですが、データセットが変更されていない場合でも、複数回実行しても結果の一貫性が保証されないことに注意してください。
> クエリによって返される行の数がクエリによって明示的に制限されていない (演算子が使用されていない) 場合でも `take` 、Kusto はその数値を既定で制限します。 詳細については、「 [Kusto クエリの制限](../concepts/querylimits.md)」を参照してください。

## <a name="syntax"></a>構文

`take`*Numberofrows* 
 `limit`*Numberofrows*

( `take` と `limit` はシノニムです)。

## <a name="paging-of-query-results"></a>クエリ結果のページング

ページングを実装する方法は次のとおりです。

* クエリの結果を外部ストレージにエクスポートし、生成されたデータをページングします。
* Kusto クエリの結果をキャッシュすることによって、ステートフルなページング API を提供する中間層アプリケーションを作成します。
* [保存されているクエリ結果](../management/stored-query-results.md#pagination)での改ページ調整を使用します。


## <a name="see-also"></a>関連項目

* [sort 演算子](sortoperator.md)
* [top 演算子](topoperator.md)
* [top-nested 演算子](topnestedoperator.md)
