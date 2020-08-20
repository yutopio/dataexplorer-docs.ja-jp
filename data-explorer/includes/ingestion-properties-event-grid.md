---
title: インクルード ファイル
description: インクルード ファイル
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 08/12/2020
ms.author: orspodek
ms.reviewer: tzgitlin
ms.custom: include file
ms.openlocfilehash: f63288ad25363f1d32184e45efb21b69a894da0c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201712"
---
|**プロパティ** | **プロパティの説明**|
|---|---|
| `rawSizeBytes` | 未加工の (圧縮されていない) データのサイズ。 Avro/ORC/Parquet の場合、これは形式固有の圧縮が適用される前のサイズです。 このプロパティを圧縮されていないデータ サイズ (バイト単位) に設定して、元のデータ サイズを指定します。|
| `kustoTable` |  既存のターゲット テーブルの名前。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| `kustoDataFormat` |  データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| `kustoIngestionMappingReference` |  使用する既存のインジェスト マッピングの名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| `kustoIgnoreFirstRecord` | `true` に設定した場合、Kusto で BLOB の最初の行が無視されます。 表形式のデータ (CSV、TSV など) でヘッダーを無視するために使用します。 |
| `kustoExtentTags` | 結果のエクステントに添付される[タグ](../kusto/management/extents-overview.md#extent-tagging)を表す文字列。 |
| `kustoCreationTime` |  ISO 8601 の文字列として書式設定されている、BLOB の [$IngestionTime](../kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) をオーバーライドします。 バックフィルに使用します。 |
