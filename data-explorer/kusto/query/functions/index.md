---
title: 関数 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c6d9aedee17592ac1eb1b43e93dead80eb9fc61
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128854"
---
# <a name="function-types"></a>関数の種類

**関数**は、再利用可能なクエリまたはクエリ部分です。 Kusto は、次のようないくつかの種類の関数をサポートしています。

* **ストアド関数**は、データベースのスキーマ エンティティの 1 つの種類として格納および管理されるユーザー定義関数です。
  「[ストアド関数](../schema-entities/stored-functions.md)」を参照してください。
* **クエリ定義関数**は、1 つのクエリの範囲内で定義および使用されるユーザー定義関数です。 このような関数の定義は、[let ステートメント](../letstatement.md)を使用して行います。
  「[ユーザー定義関数](./user-defined-functions.md)」を参照してください。
* **組み込み関数**は、ハードコーディングされています (Kusto によって定義されており、ユーザーが変更することはできません)。