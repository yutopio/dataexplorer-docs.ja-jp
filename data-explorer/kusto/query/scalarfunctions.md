---
title: スカラー関数-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのスカラー関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 640c331b177642735d875f615772dcdbb6ec68d8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128871"
---
# <a name="scalar-function-types"></a>スカラー関数の種類

## <a name="binary-functions"></a>Binary 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|2つの値の間のビットごとの and 演算の結果を返します。|
|[binary_not()](binary-notfunction.md)|入力値のビットごとの否定を返します。|
|[binary_or()](binary-orfunction.md)|2つの値のビットごとの or 演算の結果を返します。|
|[binary_shift_left()](binary-shift-leftfunction.md)|2つの数値のペアに対してバイナリシフトの左操作を返します。 a << n です。|
|[binary_shift_right()](binary-shift-rightfunction.md)|2つの数値のペアに対して、バイナリシフトの右操作を返します。 a >> n です。|
|[binary_xor()](binary-xorfunction.md)|2つの値のビットごとの xor 演算の結果を返します。|
|[bitset_count_ones()](bitset-count-onesfunction.md)|数値のバイナリ表現で設定されたビット数を返します。|

## <a name="conversion-functions"></a>変換関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|入力をブール値 (符号付き8ビット) 表現に変換します。|
|[todatetime()](todatetimefunction.md)|入力を datetime スカラーに変換します。|
|[todouble ()/再生 ()](todoublefunction.md)|入力を real 型の値に変換します。 (todouble () と、は、シノニムです)。|
|[tostring()](tostringfunction.md)|入力を文字列形式に変換します。|
|[totimespan()](totimespanfunction.md)|入力を timespan スカラーに変換します。|

## <a name="datetimetimespan-functions"></a>DateTime/timespan 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|現在の UTC 時刻から指定された期間を減算します。|
|[datetime_add()](datetime-addfunction.md)|指定された datetime に加算された、指定した datepart から指定された量を乗算した新しい datetime を計算します。|
|[datetime_part()](datetime-partfunction.md)|要求された日付部分を整数値として抽出します。|
|[datetime_diff()](datetime-difffunction.md)|指定されている場合、オフセットでシフトした日付を含む年の最後の日付を返します。|
|[dayofmonth()](dayofmonthfunction.md)|指定された月の日を表す整数値を返します。|
|[dayofweek()](dayofweekfunction.md)|前の日曜日からの日数を timespan として返します。|
|[dayofyear()](dayofyearfunction.md)|指定された年の日番号を表す整数を返します。|
|[endofday()](endofdayfunction.md)|指定されている場合、オフセットでシフトされた日付を含む日の終わりを返します。|
|[endofmonth()](endofmonthfunction.md)|指定されている場合、オフセットでシフトした日付を含む月の終わりを返します。|
|[endofweek()](endofweekfunction.md)|指定されている場合、オフセットでシフトされた日付を含む週の終わりを返します。|
|[endofyear()](endofyearfunction.md)|指定されている場合、オフセットでシフトした日付を含む年の最後の日付を返します。|
|[format_datetime()](format-datetimefunction.md)|書式パターンパラメーターに基づいて、datetime パラメーターを書式設定します。|
|[format_timespan()](format-timespanfunction.md)|Format pattern パラメーターに基づいて、形式の timespan パラメーターを書式設定します。|
|[getmonth()](getmonthfunction.md)|datetime から月 (1 ～ 12) を取得します。|
|[getyear()](getyearfunction.md)|Datetime 引数の年の部分を返します。|
|[hourofday()](hourofdayfunction.md)|指定された日付の時間番号を表す整数値を返します。|
|[make_datetime()](make-datetimefunction.md)|指定された日付と時刻から datetime スカラー値を作成します。|
|[make_timespan()](make-timespanfunction.md)|指定された期間から timespan スカラー値を作成します。|
|[monthofyear()](monthofyearfunction.md)|指定された年の月番号を表す整数を返します。|
|[now()](nowfunction.md)|現在の UTC クロック時刻を返します。オプションで、指定した timespan でオフセットします。|
|[startofday()](startofdayfunction.md)|指定されている場合、オフセットによってシフトされた日付を含む日の開始を返します。|
|[startofmonth()](startofmonthfunction.md)|指定されている場合、オフセットによってシフトされた日付を含む月の開始を返します。|
|[startofweek()](startofweekfunction.md)|指定されている場合、オフセットでシフトされた日付を含む週の開始を返します。|
|[startofyear()](startofyearfunction.md)|指定されている場合、オフセットによってシフトされた日付を含む年の開始を返します。|
|[todatetime()](todatetimefunction.md)|入力を datetime スカラーに変換します。|
|[totimespan()](totimespanfunction.md)|入力を timespan スカラーに変換します。|
|[weekofyear()](weekofyearfunction.md)|週番号を表す整数を返します。|


## <a name="dynamicarray-functions"></a>動的/配列関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|複数の動的配列を連結して1つの配列にします。|
|[array_iif()](arrayifffunction.md)|配列に要素ごとの iif 関数を適用します。|
|[array_index_of()](arrayindexoffunction.md)|指定した項目を配列内で検索し、その位置を返します。|
|[array_length()](arraylengthfunction.md)|動的配列内の要素の数を計算します。|
|[array_slice()](arrayslicefunction.md)|動的配列のスライスを抽出します。|
|[array_split()](arraysplitfunction.md)|入力配列から分割された配列の配列を構築します。|
|[bag_keys()](bagkeysfunction.md)|動的プロパティバッグオブジェクト内のすべてのルートキーを列挙します。|
|[pack()](packfunction.md)|名前と値のリストから動的オブジェクト (プロパティバッグ) を作成します。|
|[pack_all()](packallfunction.md)|表形式の式のすべての列から動的オブジェクト (プロパティバッグ) を作成します。|
|[pack_array()](packarrayfunction.md)|すべての入力値を動的配列にパックします。|
|[repeat()](repeatfunction.md)|一連の等しい値を保持する動的配列を生成します。|
|[set_difference()](setdifferencefunction.md)|最初の配列にあるが、他の配列には存在しない個別の値のセットの配列を返します。|
|[set_has_element()](sethaselementfunction.md)|指定した配列に指定した要素が格納されているかどうかを判断します。|
|[set_intersect()](setintersectfunction.md)|すべての配列に含まれるすべての個別の値のセットの配列を返します。|
|[set_union()](setunionfunction.md)|指定された配列に含まれるすべての個別の値のセットの配列を返します。|
|[treepath()](treepathfunction.md)|動的オブジェクトのリーフを識別するパス式をすべて列挙します。|
|[zip()](zipfunction.md)|Zip 関数は、任意の数の動的配列を受け入れます。 各要素が同じインデックスの入力配列の要素を持つ配列である配列を返します。|


## <a name="window-scalar-functions"></a>ウィンドウスカラー関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[next()](nextfunction.md)|シリアル化された行セットの場合、はオフセットに従って、後の行から指定された列の値を返します。|
|[prev()](prevfunction.md)|シリアル化された行セットの場合、はオフセットに従って前の行から指定された列の値を返します。|
|[row_cumsum()](rowcumsumfunction.md)|列の累積合計を計算します。|
|[row_number()](rownumberfunction.md)|指定されたインデックスから始まる連続した数値、または既定で1から始まる、シリアル化された行セット内の行の番号を返します。|

## <a name="flow-control-functions"></a>フロー制御関数

|関数名            |説明                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|評価された式のスカラー定数値を返します。|

## <a name="mathematical-functions"></a>数学関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|入力の絶対値を計算します。|
|[acos()](acosfunction.md)|コサインが指定された数値 (cos () の逆演算) である角度を返します。|
|[asin()](asinfunction.md)|サインが指定された数値 (sin () の逆演算) である角度を返します。|
|[atan()](atanfunction.md)|タンジェントが指定された数 (tan () の逆演算) である角度を返します。|
|[atan2()](atan2function.md)|正の x 軸と、原点から点 (y, x) までの射線との角度をラジアン単位で計算します。|
|[beta_cdf()](beta-cdffunction.md)|標準の累積ベータ分布関数を返します。|
|[beta_inv()](beta-invfunction.md)|ベータ累積確率のベータ密度関数の逆の値を返します。|
|[beta_pdf()](beta-pdffunction.md)|確率密度のベータ関数を返します。|
|[cos()](cosfunction.md)|コサイン関数を返します。|
|[cot()](cotfunction.md)|指定した角度の三角関数コタンジェントをラジアン単位で計算します。|
|[degrees()](degreesfunction.md)|ラジアン単位の角度の値を度数で値に変換します。数式の角度 = (180/PI) * ラジアンで表します。|
|[exp()](exp-function.md)|X の底 e の指数関数。 e は、power x: e ^ x に発生します。|
|[exp10()](exp10-function.md)|X の底10の指数関数。これは、power x:10 ^ x に10を累乗したものです。|
|[exp2()](exp2-function.md)|X の底2の指数関数。2は、power x: 2 ^ x に発生します。|
|[gamma()](gammafunction.md)|ガンマ関数を計算します。|
|[hash()](hashfunction.md)|入力値のハッシュ値を返します。|
|[hash_combine()](hash_combinefunction.md)|2つ以上のハッシュ値を結合します。|
|[hash_many()](hash_manyfunction.md)|複数の値の結合されたハッシュ値を返します。|
|[isfinite()](isfinitefunction.md)|入力が有限値である (無限または NaN ではない) かどうかを返します。|
|[isinf()](isinffunction.md)|入力が無限 (正または負) の値であるかどうかを返します。|
|[isnan()](isnanfunction.md)|入力が非数 (NaN) 値かどうかを返します。|
|[log()](log-function.md)|自然対数関数を返します。|
|[log10()](log10-function.md)|常用対数関数を返します。|
|[log2()](log2-function.md)|底2の対数関数を返します。|
|[loggamma()](loggammafunction.md)|ガンマ関数の絶対値のログを計算します。|
|[not()](notfunction.md)|Bool 引数の値を反転させます。|
|[pi()](pifunction.md)|Pi (π) の定数値を返します。|
|[pow()](powfunction.md)|を累乗した結果を返します。|
|[radians()](radiansfunction.md)|数式ラジアン = (PI/180) * 角度 (°) を使用して、角度の角度を度数で値に変換します。|
|[rand()](randfunction.md)|乱数を返します。|
|[range()](rangefunction.md)|等間隔に並んだ一連の値を保持する動的配列を生成します。|
|[round()](roundfunction.md)|丸められたソースを指定した有効桁数に戻します。|
|[sign()](signfunction.md)|数値式の符号。|
|[sin()](sinfunction.md)|サイン関数を返します。|
|[sqrt()](sqrtfunction.md)|平方根関数を返します。|
|[tan()](tanfunction.md)|タンジェント関数を返します。|
|[welch_test()](welch-testfunction.md)|は、の p-[検定関数](https://en.wikipedia.org/wiki/Welch%27s_t-test)の値を計算します。|

## <a name="metadata-functions"></a>メタデータ関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|列名を文字列および既定値として取得します。 存在する場合は、その列への参照を返します。それ以外の場合は、既定値を返します。|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|クエリを実行している現在のクラスターを返します。|
|[current_database()](current-database-function.md)|スコープ内のデータベースの名前を返します。|
|[current_principal()](current-principalfunction.md)|このクエリを実行している現在のプリンシパルを返します。|
|[current_principal_details()](current-principal-detailsfunction.md)|クエリを実行しているプリンシパルの詳細を返します。|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|クエリを実行している現在のプリンシパルのグループメンバーシップまたはプリンシパル id を確認します。|
|[cursor_after()](cursorafterfunction.md)|カーソルの前の値の後に取り込まれたされたレコードにアクセスするために使用します。|
|[estimate_data_size()](estimate-data-sizefunction.md)|表形式の式で選択されている列の推定データサイズを返します。|
|[extent_id()](extentidfunction.md)|現在のレコードが存在するデータシャード ("エクステント") を識別する一意の識別子を返します。|
|[extent_tags()](extenttagsfunction.md)|現在のレコードが存在するデータシャード ("extent") のタグを持つ動的配列を返します。|
|[ingestion_time()](ingestiontimefunction.md)|レコードの $IngestionTime 非表示の datetime 列、または null を取得します。|

## <a name="rounding-functions"></a>丸め関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|値を切り捨てて、指定された bin サイズの倍数である整数にします。|
|[bin_at()](binatfunction.md)|ビンの開始点を制御して、値を固定サイズの "ビン" に切り捨てます。 (「Bin 関数」も参照してください)。|
|[ceiling()](ceilingfunction.md)|指定した数値式以上の最小の整数を計算します。|
|[floor()](floorfunction.md)|値を切り捨てて、指定された bin サイズの倍数である整数にします。|

## <a name="conditional-functions"></a>条件関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[case()](casefunction.md)|述語の一覧を評価し、述語が満たされている最初の結果式を返します。|
|[coalesce()](coalescefunction.md)|式のリストを評価し、null 以外の最初の式 (文字列の場合は空でない) を返します。|
|[iif ()/iff ()](iiffunction.md)|1番目の引数 (述語) を評価し、述語が true (second) または false (3 番目) のどちらに評価されたかに応じて、2番目または3番目の引数の値を返します。|
|[max_of()](max-offunction.md)|複数の評価された数値式の最大値を返します。|
|[min_of()](min-offunction.md)|複数の評価された数値式の最小値を返します。|

## <a name="series-element-wise-functions"></a>系列の要素ごとの関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|2つの数値系列入力の要素ごとの加算を計算します。|
|[series_divide()](series-dividefunction.md)|2つの数値系列入力の要素ごとの除算を計算します。|
|[series_equals()](series-equalsfunction.md)|`==`2 つの数値系列入力の要素ごとの等号 () 論理演算を計算します。|
|[series_greater()](series-greaterfunction.md)|`>`2 つの数値系列入力の要素ごとに大きい () 論理演算を計算します。|
|[series_greater_equals()](series-greater-equalsfunction.md)|`>=`2 つの数値系列入力の要素ごとのより大きいまたは等しい () 論理演算を計算します。|
|[series_less()](series-lessfunction.md)|`<`2 つの数値系列入力の要素ごとの less () 論理演算を計算します。|
|[series_less_equals()](series-less-equalsfunction.md)|`<=`2 つの数値系列入力の要素ごとの () 論理演算を計算します。|
|[series_multiply()](series-multiplyfunction.md)|2つの数値系列入力の要素ごとの乗算を計算します。|
|[series_not_equals()](series-not-equalsfunction.md)|`!=`2 つの数値系列入力の要素ごとの not equals () 論理演算を計算します。|
|[series_subtract()](series-subtractfunction.md)|2つの数値系列入力の要素ごとの減算を計算します。|

## <a name="series-processing-functions"></a>系列処理関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|系列をコンポーネントに分解します。|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|系列分解に基づいて系列の異常を検出します。|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|系列分解に基づく予測。|
|[series_fill_backward()](series-fill-backwardfunction.md)|系列内の欠損値の後方塗りつぶし補間を実行します。|
|[series_fill_const()](series-fill-constfunction.md)|系列の欠損値を指定された定数値に置き換えます。|
|[series_fill_forward()](series-fill-forwardfunction.md)|系列内の欠損値の前方フィル補間を実行します。|
|[series_fill_linear()](series-fill-linearfunction.md)|系列内の欠損値の線形補間を実行します。|
|[series_fir()](series-firfunction.md)|系列に有限インパルス応答フィルターを適用します。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|系列に2つのセグメントの線形回帰を適用し、複数の列を返します。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|系列に2つのセグメントの線形回帰を適用し、動的オブジェクトを返します。|
|[series_fit_line()](series-fit-linefunction.md)|系列に線形回帰を適用し、複数の列を返します。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|系列に線形回帰を適用し、動的オブジェクトを返します。|
|[series_iir()](series-iirfunction.md)|無限インパルス応答フィルターを系列に適用します。|
|[series_outliers()](series-outliersfunction.md)|系列内の異常点をスコア付けします。|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|2つの系列のピアソンの相関係数を計算します。|
|[series_periods_detect()](series-periods-detectfunction.md)|時系列に存在する最も重要な期間を検索します。|
|[series_periods_validate()](series-periods-validatefunction.md)|時系列に特定の長さの定期的なパターンが含まれているかどうかを確認します。|
|[series_seasonal()](series-seasonalfunction.md)|系列の季節成分を検索します。|
|[series_stats()](series-statsfunction.md)|複数の列に含まれる系列の統計を返します。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|動的オブジェクトの系列の統計を返します。|

## <a name="string-functions"></a>文字列関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|文字列を base64 文字列としてエンコードします。|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|Base64 文字列を UTF-8 文字列にデコードします。|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|Base64 文字列を長整数値の配列にデコードします。|
|[countof ()](cotfunction.md)|文字列内の部分文字列の出現回数をカウントします。 文字列の一致が重複する可能性があります。regex は一致しません。|
|[extract()](extractfunction.md)|テキスト文字列から 正規表現 との一致を取得します。|
|[extract_all()](extractallfunction.md)|テキスト文字列から正規表現のすべての一致を取得します。|
|[extractjson()](extractjsonfunction.md)|パス式を使用している JSON テキストから、指定された要素を取得します。|
|[indexof()](indexoffunction.md)|関数は、入力文字列内で指定した文字列が最初に見つかった位置の0から始まるインデックスを報告します。|
|[isempty()](isemptyfunction.md)|引数が空の文字列であるか、または null である場合に true を返します。|
|[isnotempty()](isnotemptyfunction.md)|引数が空の文字列または null でない場合に true を返します。|
|[isnotnull ()](isnotnullfunction.md)|引数が null でない場合は true を返します。|
|[isnull()](isnullfunction.md)|唯一の引数を評価し、引数が null 値に評価されるかどうかを示すブール値を返します。|
|[parse_csv()](parsecsvfunction.md)|コンマ区切り値を表す特定の文字列を分割し、これらの値を含む文字列配列を返します。|
|[parse_ipv4()](parse-ipv4function.md)|入力を long (符号付き64ビット) 数値表現に変換します。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|入力文字列と IP プレフィックスマスクを long (符号付き64ビット) 数値表現に変換します。|
|[parse_ipv6()](parse-ipv6function.md)|IPv6 または IPv4 文字列を正規の IPv6 文字列形式に変換します。|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|IPv6 または IPv4 の文字列とネットマスクを正規の IPv6 文字列形式に変換します。|
|[parse_json()](parsejsonfunction.md)|は文字列を JSON 値として解釈し、値を動的として返します。|
|[parse_url()](parseurlfunction.md)|絶対 URL 文字列を解析し、URL のすべての部分を含む動的オブジェクトを返します。|
|[parse_urlquery()](parseurlqueryfunction.md)|Url クエリ文字列を解析し、動的オブジェクトにクエリパラメーターが含まれていることを返します。|
|[parse_version()](parse-versionfunction.md)|バージョンの入力文字列形式を、比較可能な10進数に変換します。|
|[replace()](replacefunction.md)|正規表現のすべての一致を別の文字列に置き換えます。|
|[reverse()](reversefunction.md)|関数は、入力文字列を逆にします。|
|[split()](splitfunction.md)|指定された区切り記号に従って指定された文字列を分割し、含まれている部分文字列を含む文字列配列を返します。|
|[strcat()](strcatfunction.md)|1 ~ 64 の引数を連結します。|
|[strcat_delim()](strcat-delimfunction.md)|最初の引数として指定された区切り記号を使用して、2 ~ 64 の引数を連結します。|
|[strcmp()](strcmpfunction.md)|2 つの文字列を比較します。|
|[strlen()](strlenfunction.md)|入力文字列の長さを文字数で返します。|
|[strrep()](strrepfunction.md)|指定された文字列を繰り返す回数 (既定値-1)。|
|[substring()](substringfunction.md)|あるインデックスから文字列の末尾までの位置から、ソース文字列から部分文字列を抽出します。|
|[toupper()](toupperfunction.md)|文字列を大文字に変換します。|
|[translate()](translatefunction.md)|指定された文字列の文字セット (' searchList ') を別の文字セット (' replacementList ') に置換します。|
|[trim()](trimfunction.md)|指定した正規表現の先頭と末尾の一致をすべて削除します。|
|[trim_end()](trimendfunction.md)|指定した正規表現の末尾の一致を削除します。|
|[trim_start()](trimstartfunction.md)|指定された正規表現の先頭の一致を削除します。|
|[url_decode()](urldecodefunction.md)|関数は、エンコードされた URL を通常の URL 表現に変換します。|
|[url_encode()](urlencodefunction.md)|関数は、入力 URL の文字をインターネット経由で送信できる形式に変換します。|

## <a name="ipv4ipv6-functions"></a>IPv4/IPv6 機能

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|2つの IPv4 文字列を比較します。|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|2つの IPv4 文字列を一致と見なします。|
|[parse_ipv4()](parse-ipv4function.md)|入力文字列を long (符号付き64ビット) 数値表現に変換します。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|入力文字列と IP プレフィックスマスクを long (符号付き64ビット) 数値表現に変換します。|
|[ipv6_compare()](ipv6-comparefunction.md)|2つの IPv4 または IPv6 文字列を比較します。|
|[ipv6_is_match()](ipv6-is-matchfunction.md)|2つの IPv4 または IPv6 文字列を照合します。|
|[parse_ipv6()](parse-ipv6function.md)|IPv6 または IPv4 文字列を正規の IPv6 文字列形式に変換します。|
|[parse_ipv6_mask()](parse-ipv6-maskfunction.md)|IPv6 または IPv4 の文字列とネットマスクを正規の IPv6 文字列形式に変換します。|

## <a name="type-functions"></a>Type 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|1つの引数のランタイム型を返します。|

## <a name="scalar-aggregation-functions"></a>スカラー集計関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|Hll の結果 (hll または hll-merge によって生成されたもの) を計算します。|
|[hll_merge()](hllmergefunction.md)|Hll の結果をマージします (集計バージョン hll-merge () のスカラーバージョン)。|
|[percentile_tdigest()](percentile-tdigestfunction.md)|Tdigest の結果 (tdigest または merge-tdigest によって生成されたもの) からの百分位数を計算します。|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|データセット内の値の割合の順位を計算します。|
|[rank_tdigest()](rank-tdigest.md)|セット内の値の相対的な順位を計算します。|
|[tdigest_merge()](tdigest-mergefunction.md)|Tdigest の結果をマージします (集計バージョンのスカラーバージョン tdigest-merge ())。|

## <a name="geospatial-functions"></a>地理空間の関数

|関数名|説明|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|地球上の2つの地理空間座標間の最短距離を計算します。|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|Geohash 四角形領域の中心を表す地理空間座標を計算します。|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|空間座標が地球上の円の内側にあるかどうかを計算します。|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|地理的な場所の Geohash 文字列値を計算します。|
