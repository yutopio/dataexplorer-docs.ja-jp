---
title: Kusto-Azure データエクスプローラーの同期
description: この記事では、Azure データエクスプローラーでの Kusto の同期について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: zivc
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/12/2019
ms.openlocfilehash: 6e1cd00cb84ad7b4d62429a932d3ffe298a83766
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875193"
---
# <a name="sync-kusto"></a>Kusto の同期

Sync Kusto は、テーブルスキーマやストアド関数など、さまざまな Kusto スキーマエンティティをユーザーが同期できるツールです。 この同期は、ローカルファイルシステム、Azure データエクスプローラーデータベース、Azure Dev Ops リポジトリの間で行われます。

Kusto の同期に [ついては、GitHub を](https://github.com/microsoft/synckusto)参照してください。