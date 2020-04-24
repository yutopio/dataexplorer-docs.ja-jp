---
title: 日時データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの日時データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5a234c49a5bb5b04ccde49e7fb3702d927e38b5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509913"
---
# <a name="the-datetime-data-type"></a>日時データ型

( `datetime` `date`) データ型は、一般に日付と時刻として表される、時刻の瞬間を表します。
値の範囲は 00:00:00(午前0時)、0001年1月1日(一般)のアンノ・ドミニ(一般時代)から11:59:59 P.M.まで、9999年12月31日(米国) (C.E.)グレゴリオ暦で表示されます。 

時間の値はティックと呼ばれる 100 ナノ秒単位で測定され、特定の日付は、午前 0 時(0001 年 1 月 1 日)の午前 0 時以降のティック数です。 (C.E.)グレゴリオ暦カレンダー (うるう秒で追加されるティックを除く)
たとえば、ティック値が 3124137600000000000000 の場合は、日付 (1 月 1 日 1 日 1 日午前 0 時 10:00) を表します。
これは「線形時間の瞬間」と呼ばれることもあります。

> [!WARNING]
> Kusto の`datetime`値は常に UTC タイム ゾーンにあります。 他の`datetime`タイム ゾーンの値を表示する場合は、データ自体のプロパティではなく、データを表示するユーザー アプリケーションの役割です。 タイム ゾーン値をデータの一部として保持する必要がある場合は、別の列を使用する必要があります (UTC に対するオフセット情報を提供)。

## <a name="datetime-literals"></a>datetime リテラル

`datetime`型のリテラルには、次の`datetime(`表で示すように、value*に対*していくつかの形式がサポートされている構文*値*`)`があります。

|例                                                     |値                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|時刻は常に UTC 形式です。 日付を省略すると、今日の時刻になります。|
|`datetime(null)`                                            |[「null 値](null-values.md)」を参照してください。                            |
|`now()`                                                     |現在の時刻。                                             |
|`now(`-*Timespan*`)`                                        |`now()-`*Timespan*                                            |
|`ago(`*Timespan*`)`                                         |`now()-`*Timespan*                                            |

`now()`を`ago()`クリックし`datetime`、Kusto がクエリを実行し始めた時点と比較した値を示します。 これらは同じクエリ内で複数回出現する可能性があり、それらすべてに 1 つの値が使用されます。
(つまり、式は`now(-x) - ago(x)`常にゼロの`timespan`値に評価されます。

## <a name="supported-formats"></a>サポートされるフォーマット

この場合`datetime`[、datetime() リテラル](#datetime-literals)および[todatetime()](../todatetimefunction.md)関数としてサポートされる形式がいくつかあります。

> [!WARNING]
> ISO 8601 形式のみを使用**することを強くお勧めします**。

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|例|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s"|2014-05-25T08:20:03.123456|
|%Y%m-%dT%H:%M"|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z"|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s"|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M"|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|例|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|11月14日(日) 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|11月14日(日) 15:05:02|
|%w,%e %b %r %H:%M|11月14日(日) 15:05|
|%w, %e %b %r %H:%M %Z|11月14日 15:05 GMT|
|%e %b %r %R %H:%M:%s %Z|11月14日 15:05:02 GMT|
|%e %b %r %H:%M:%s"|11月14日 15:05:02|
|%e %b %r %H:%M"|11月14日 15:05|
|%e %b %r %R %H:%M %Z"|11月14日 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|例|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|土曜日, 08-11-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|土曜日, 08-11月-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|土曜日, 08-11月-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|土曜日, 08-11月-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-11-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s"|08-11月14日 15:05:02|
|%e-%b-%r %H:%M %Z"|08-11-14 15:05 GMT|
|%e-%b-%r %H:%M"|08-11月14日 15:05|


### <a name="sortable"></a>Sortable 

|Format|例|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z"|2014-11-08T15:05 GMT|