---
title: 具体化 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの具体化 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: 0cf510a8a7a6042d99587b51ee62eb30d4b7b7a7
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271300"
---
# <a name="materialize"></a>materialize()

他のサブクエリが部分的な結果を参照できるように、クエリの実行時にサブクエリの結果をキャッシュできます。

 
**構文**

`materialize(`*expression*`)`

**引数**

* *式*: クエリの実行中に評価およびキャッシュされる表形式の式。

**ヒント**

* オペランドに1回だけ実行できる相互サブクエリがある場合は、結合または共用体で具体化を使用します。 次の例を参照してください。

* また、join/union の分岐が必要な場合にも役立ちます。

* 具現化は、キャッシュされた結果に名前を指定した場合にのみ、let ステートメントで使用できます。


* 具体化のキャッシュサイズは**5 GB**です。 
  この制限はクラスターノードごとに行われ、同時に実行されるすべてのクエリに共通です。
  クエリでが使用され `materialize()` ていて、それ以上のデータをキャッシュが保持できない場合、クエリはエラーで中止されます。

**使用例**

ランダムな値のセットを生成し、次のことを知りたいと思います。 
 * 個別の値の数 
 * これらのすべての値の合計 
 * 上位3つの値

この操作は[バッチ](batches.md)と具体化を使用して行うことができます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
let randomSet = materialize(range x from 1 to 30000000 step 1
| project value = rand(10000000));
randomSet
| summarize dcount(value);
randomSet
| top 3 by value;
randomSet
| summarize sum(value)

```
