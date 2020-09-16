---
title: Datetime データ型-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの datetime データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a1c4b89677e08872fdf81e72f7391511b7d6c637
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680716"
---
# <a name="the-datetime-data-type"></a>Datetime データ型

`datetime`( `date` ) データ型は、通常、日付と時刻として表現される瞬間を表します。
値の範囲は、00:00:00 (午前0時)、0001キリスト Domini (共通時代 (年号)、11:59:59 pm、9999 A.D. 年12月31日です。 西暦グレゴリオ暦。 

時刻値は、タイマー刻みと呼ばれる100ナノ秒単位で測定されます。特定の日付は、0001年1月1日午前0時12:00 からのタイマー刻みの数です。 西暦いる gregoriancalendar カレンダー (うるう年によって追加されるティックを除く)。
たとえば、ティック値31241376000000000は、日付、金曜日、1月01、0100 12:00:00 深夜を表します。
これは、"線形時間における瞬間" と呼ばれることもあります。

> [!WARNING]
> `datetime`Kusto の値は、常に UTC タイムゾーンになります。 `datetime`他のタイムゾーンでの値の表示は、データ自体のプロパティではなく、データを表示するユーザーアプリケーションの役割です。 タイムゾーン値をデータの一部として保持する必要がある場合は、別の列を使用する必要があります (UTC を基準としたオフセット情報が提供されます)。

## <a name="datetime-literals"></a>datetime リテラル

型のリテラルには `datetime` `datetime(` *value* `)` 、次の表に示すように、*値*に対して複数の形式がサポートされている構文値があります。

|例                                                     |値                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|時刻は常に UTC 形式です。 日付を省略すると、今日の時刻になります。|
|`datetime(null)`                                            |「 [Null 値](null-values.md)」を参照してください。                            |
|`now()`                                                     |現在の時刻。                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` とは、 `ago()` `datetime` Kusto がクエリの実行を開始した時点と比較した値を示します。 これらは、同じクエリ内で複数回出現する可能性があり、すべての値に1つの値が使用されます。
(つまり、などの式は `now(-x) - ago(x)` 常に値0に評価さ `timespan` れます)。

## <a name="supported-formats"></a>サポートされるフォーマット

には、 `datetime` [datetime () リテラル](#datetime-literals) および [todatetime ()](../todatetimefunction.md) 関数としてサポートされる形式がいくつかあります。

> [!WARNING]
> ISO 8601 形式のみを使用する **ことを強くお勧め** します。

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|例|
|------|-------|
|% Y-% m-% dT% H:%M:% s% z|2014-05-25T08:20: 03.123456 Z|
|% Y-% m-% dT% H:%M:% s|2014-05-25T08:20: 03.123456|
|% Y-% m-% dT% H:%M|2014-05-25T08:20|
|% Y-% m-% d% H:%M:% s% z|2014-11-08 15:55: 55.123456 z|
|% Y-% m-% d% H:%M:% s|2014-11-08 15:55:55|
|% Y-% m-% d% H:%M|2014-11-08 15:55|
|% Y-% m-% d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|例|
|------|-------|
|% w、% e% b% r% H:%M:% s% Z|14 15:05:02 年11月8日 (GMT)|
|% w、% e% b% r% H:%M:% s|14 15:05:02 年11月8日|
|% w、% e% b% r% H:%M|14 15:05 年11月8日|
|% w、% e% b% r% H:%M% Z|14 15:05 年11月8日 (GMT)|
|% e% b% r% H:%M:% s% Z|8月8日 14 15:05:02 GMT|
|% e% b% r% H:%M:% s|14 15:05:02 年11月8日|
|% e% b% r% H:%M|14 15:05 年11月8日|
|% e% b% r% H:%M% Z|8月8日 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|例|
|------|-------|
|% w、% e-% b-% r% H:%M:% s% Z|土曜日、08-11 月 14 15:05:02 GMT|
|% w、% e-% b-% r% H:%M:% s|土曜日、08-11 月-14 15:05:02|
|% w、% e-% b-% r% H:%M% Z|土曜日、08-11 月 14 15:05 GMT|
|% w、% e-% b-% r% H:%M|土曜日、08-11 月-14 15:05|
|% e-% b-% r% H:%M:% s% Z|08-11 月 14 15:05:02 ~ GMT|
|% e-% b-% r% H:%M:% s|08 ~ 11 月-14 15:05:02|
|% e-% b-% r% H:%M% Z|08-11 月 14 15:05 ~ GMT|
|% e-% b-% r% H:%M|08 ~ 11 月-14 15:05|


### <a name="sortable"></a>日付 

|Format|例|
|------|-------|        
|% Y-% n-% e% H:%M:% s|2014-11-08 15:05:25|
|% Y-% n-% e% H:%M:% s% Z|2014-11-08 15:05:25 GMT|
|% Y-% n-% e% H:%M|2014-11-08 15:05|
|% Y-% n-% e% H:%M% Z|2014-11-08 15:05 GMT|
|% Y-% n-% eT% H:%M:% s|2014-11-08T15:05:25|
|% Y-% n-% eT% H:%M:% s% Z|2014-11-08T15:05:25 GMT|
|% Y-% n-% eT% H:%M|2014-11-08T15:05|
|% Y-% n-% eT% H:%M% Z|2014-11-08T15:05 GMT|
