---
title: datetime データ型 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の datetime データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513133"
---
# <a name="the-datetime-data-type"></a>datetime データ型

`datetime` (`date`) データ型は、特定の時点を表します。通常は日時として表現されます。
値の範囲は、西暦 (グレゴリオ暦) 紀元 0001 年 1 月 １ 日 00:00:00 (深夜) から西暦紀元 9999 年 12 月 31 日午後 11:59:59 です。 

時刻の値は、タイマー刻みと呼ばれる100 ナノ秒単位で測定され、特定の日付は、西暦 (グレゴリオ暦) 紀元 0001 年 1 月 1 日深夜 12:00 からの タイマー刻みの数です (うるう秒によって追加されたタイマー刻みは除外されます)。
たとえば、タイマー刻み値 31241376000000000 は、0100 年 1 月 1 日 (金曜日) 深夜 12:00:00 を表します。
これは、"線形時間における瞬間" と呼ばれることもあります。

> [!WARNING]
> Kusto の `datetime` 値は、常に UTC タイム ゾーンになります。 他のタイム ゾーンで `datetime` 値を表示することは、データ自体のプロパティではなく、データを表示するユーザー アプリケーションの責任です。 タイム ゾーン値をデータの一部として保持する必要がある場合は、別の列を使用する必要があります (UTC に対するオフセット情報を指定します)。

## <a name="datetime-literals"></a>datetime リテラル

`datetime` 型のリテラルの構文は `datetime(`*value*`)` です。*value* でサポートされる数値の形式を次の表に示します。

|例                                                     |値                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|時刻は常に UTC 形式です。 日付を省略すると、今日の時刻になります。|
|`datetime(null)`                                            |「[null 値](null-values.md)」を参照してください。                            |
|`now()`                                                     |現在の時刻。                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` と `ago()` は、Kusto がクエリの実行を開始した時点と比較した `datetime` 値を示します。 これらは、同じクエリ内で複数回出現することができ、すべての値で単一の値が使用されます
(つまり、`now(-x) - ago(x)` などの式は、常に `timespan` 値 0 に対して評価されます)。

## <a name="supported-formats"></a>サポートされるフォーマット

[datetime() リテラル](#datetime-literals)と [todatetime()](../todatetimefunction.md) 関数としてサポートされている `datetime` のさまざまな形式があります。

> [!WARNING]
> ISO 8601 形式のみを使用することを **強くお勧めします**。

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|例|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|例|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|Sat, 8 Nov 14 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|Sat, 8 Nov 14 15:05:02|
|%w, %e %b %r %H:%M|Sat, 8 Nov 14 15:05|
|%w, %e %b %r %H:%M %Z|Sat, 8 Nov 14 15:05 GMT|
|%e %b %r %H:%M:%s %Z|8 Nov 14 15:05:02 GMT|
|%e %b %r %H:%M:%s|8 Nov 14 15:05:02|
|%e %b %r %H:%M|8 Nov 14 15:05|
|%e %b %r %H:%M %Z|8 Nov 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|例|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|Saturday, 08-Nov-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|Saturday, 08-Nov-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|Saturday, 08-Nov-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|Saturday, 08-Nov-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-Nov-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s|08-Nov-14 15:05:02|
|%e-%b-%r %H:%M %Z|08-Nov-14 15:05 GMT|
|%e-%b-%r %H:%M|08-Nov-14 15:05|


### <a name="sortable"></a>並べ替え可能 

|Format|例|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y-%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z|2014-11-08T15:05 GMT|
