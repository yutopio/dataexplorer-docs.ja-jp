---
title: ipv4_lookup プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの ipv4_lookup プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: ec826e7d16e575fa4904aba93575ab7e605d2b18
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511154"
---
# <a name="ipv4_lookup-plugin"></a>ipv4_lookup プラグイン

プラグインは、 `ipv4_lookup` ルックアップテーブル内の IPv4 値を検索し、一致した値を持つ行を返します。

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` `ipv4_lookup(` *lookuptable* `,` *SourceIPv4Key* `,` *lookuptable* [ `,` *return_unmatched* ] `)`

## <a name="arguments"></a>引数

* *T*: 列 *SourceIPv4Key* が IPv4 照合に使用される表形式の入力。
* *Lookuptable*: ipv4 参照データを含むテーブルまたは表形式式。 ipv4 照合には列 *lookuptable* が使用されます。 IPv4 値は [、IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。
* *SourceIPv4Key*: *lookuptable* で検索する IPv4 文字列を含む *T* の列。 IPv4 値は [、IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。
* *Lookupkey*: 各 *SourceIPv4Key* 値に対して照合される、IPv4 文字列を含む *lookupkey* の列。
* *return_unmatched*: 結果にすべての行を含めるか、一致する行のみを含めるかを定義するブール型のフラグ (既定値: `false` -返された一致する行のみ)。

### <a name="ip-prefix-notation"></a>IP プレフィックスの表記
 
IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。
スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ () の右側にある数字 (1 ~ 32) `/` は、ネットマスク内の連続した1ビットの数です。 

たとえば、192.168.2.0/24 は、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask を持ちます。

## <a name="returns"></a>戻り値

この `ipv4_lookup` プラグインは、IPv4 キーに基づく結合 (lookup) の結果を返します。 テーブルのスキーマは、 [ `lookup` 演算子](lookupoperator.md)の結果と同様に、ソーステーブルと参照テーブルの和集合です。

*Return_unmatched* 引数がに設定されている場合、結果として得られるテーブルには、 `true` 一致する行と一致しない行の両方が含まれます (null が格納されます)。

*Return_unmatched* の引数がに設定されているか省略されている場合 `false` (の既定値 `false` が使用されます)、結果として得られるテーブルのレコード数は一致結果と同じになります。 このような参照は、実行と比較してパフォーマンスが優れてい `return_unmatched=true` ます。

> [!NOTE]
> * このプラグインでは、小規模なルックアップテーブルサイズ (分野行) を想定して、IPv4 ベースの結合のシナリオについて説明します。入力テーブルには、必要に応じてサイズが大きくなります。
> * プラグインのパフォーマンスは、参照とデータソースのテーブルのサイズ、列の数、および一致するレコードの数によって異なります。

## <a name="examples"></a>例

### <a name="ipv4-lookup---matching-rows-only"></a>IPv4 参照-一致する行のみ

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|ip|ネットワーク|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|ヨーロッパ|GB|イギリス|
|5.8.1.2|5.8.0.0/19|EU|ヨーロッパ|RU|ロシア|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>IPv4 参照-一致する行と一致しない行の両方を返します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|ネットワーク|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|ヨーロッパ|GB|イギリス|
|5.8.1.2|5.8.0.0/19|EU|ヨーロッパ|RU|ロシア|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>IPv4 参照-external_data () でソースを使用する

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|ネットワーク|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|ヨーロッパ|GB|イギリス|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|ヨーロッパ|RU|ロシア|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|ヨーロッパ|SE|スウェーデン|0|0|
