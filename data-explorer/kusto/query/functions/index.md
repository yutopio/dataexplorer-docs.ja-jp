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
ms.openlocfilehash: f1307b54c9f0b7a948925dd4eaa4d1f2e89d8070
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490312"
---
# <a name="functions"></a>関数

**関数**は、再利用可能なクエリまたはクエリ部分です。 Kusto は、次のようないくつかの種類の関数をサポートしています。

* **ストアド関数**は、データベースのスキーマ エンティティの 1 つの種類として格納および管理されるユーザー定義関数です。
  「[ストアド関数](../schema-entities/stored-functions.md)」を参照してください。
* **クエリ定義関数**は、1 つのクエリの範囲内で定義および使用されるユーザー定義関数です。 このような関数の定義は、[let ステートメント](../letstatement.md)を使用して行います。
  「[ユーザー定義関数](./user-defined-functions.md)」を参照してください。
* **組み込み関数**は、ハードコーディングされています (Kusto によって定義されており、ユーザーが変更することはできません)。