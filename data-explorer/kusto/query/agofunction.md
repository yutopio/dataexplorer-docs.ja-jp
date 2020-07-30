---
title: 前 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの前の () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f3d58a7b29731125407eec7429967d1f90f5b7e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349773"
---
# <a name="ago"></a>ago()

現在の UTC 時刻から指定された期間を減算します。

```kusto
ago(1h)
ago(1d)
```

`now()` と同様、この関数はステートメント内で複数回使用することができます。また、参照される UTC 時刻はすべてのインスタンスで同じになります。

## <a name="syntax"></a>構文

`ago(`*a_timespan*`)`

## <a name="arguments"></a>引数

* *a_timespan*: 現在の UTC 時刻 (`now()`) から減算する期間。

## <a name="returns"></a>戻り値

`now() - a_timespan`

## <a name="example"></a>例

過去 1 時間以内のタイムスタンプを持つすべての行は、次のようになります。

```kusto
T | where Timestamp > ago(1h)
```