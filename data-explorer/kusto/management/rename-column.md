---
title: 列の名前を変更する - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの列の名前の変更について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 02e692e01bb8eb0abd49c9673b000b722734db90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520504"
---
# <a name="rename-column"></a>列の名前を変更する

既存のテーブル列の名前を変更します。
複数の列の名前を変更するには、[以下](#rename-columns)のを参照してください。

**構文**

`.rename``column` [*データベース名*`.`]*テーブル名*`.`*列既存の名前*`to`*列新しい名前*

ここで *、データベース名*、*テーブル名*、*列の存在名*、および*列NewName*は、それぞれのエンティティの名前であり、[識別子の命名規則](../query/schema-entities/entity-names.md)に従います。

## <a name="rename-columns"></a>列名の変更

同じテーブル内の複数の既存の列の名前を変更します。

**構文**

`.rename``columns`*コル1* `=` [*データベース名*`.`[*テーブル名*`.`*コル2*]] `,` .

このコマンドを使用して、2 つの列の名前を入れ替えることができます (それぞれが他の列の名前として名前変更されます)。