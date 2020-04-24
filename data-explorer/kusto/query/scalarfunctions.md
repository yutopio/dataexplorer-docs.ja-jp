---
title: スカラー関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのスカラー関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: ef807c93f9ae9b125439f55f32a2bb1eb21d46b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509556"
---
# <a name="scalar-functions"></a>スカラー関数

## <a name="binary-functions"></a>バイナリ関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|ビットごとの結果と 2 つの値間の演算を返します。|
|[binary_not()](binary-notfunction.md)|入力値のビットごとの否定を返します。|
|[binary_or()](binary-orfunction.md)|2 つの値のビットごとの演算または演算の結果を返します。|
|[binary_shift_left()](binary-shift-leftfunction.md)|数値のペアに対する 2 進シフト左の演算 << を返します。|
|[binary_shift_right()](binary-shift-rightfunction.md)|数値のペアに対する 2 進シフト右の演算 >> を返します。|
|[binary_xor()](binary-xorfunction.md)|2 つの値のビットごとの xor 演算の結果を返します。|
|[bitset_count_ones()](bitset-count-onesfunction.md)|数値のバイナリ表現で設定されたビット数を返します。|

## <a name="conversion-functions"></a>変換関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|入力をブール (符号付き 8 ビット) 表現に変換します。|
|[todatetime()](todatetimefunction.md)|入力を日付時刻スカラーに変換します。|
|[トダブル()/トリアル()](todoublefunction.md)|入力を実数型の値に変換します。 (todouble() と toreal() は同義語です。|
|[Tostring()](tostringfunction.md)|入力を文字列形式に変換します。|
|[totimespan()](totimespanfunction.md)|入力をタイムスパン スカラーに変換します。|


## <a name="datetimetimespan-functions"></a>日時/時間間隔関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[前に()](agofunction.md)|現在の UTC 時刻から指定された期間を減算します。|
|[datetime_add()](datetime-addfunction.md)|指定した日付部分に指定した量を掛けた新しい日時を、指定した日時に加算して計算します。|
|[datetime_part()](datetime-partfunction.md)|要求された日付部分を整数値として抽出します。|
|[datetime_diff()](datetime-difffunction.md)|指定した場合、日付を含む年の終わりを返します。|
|[日の月()](dayofmonthfunction.md)|指定した月の日数を表す整数を返します。|
|[デイオブウィーク()](dayofweekfunction.md)|前の日曜日以降の整数の日数を、期間として返します。|
|[デイオブイヤー()](dayofyearfunction.md)|整数を返し、指定した年の日の番号を表します。|
|[endofday()](endofdayfunction.md)|指定した場合、日付を含む日の終わりをオフセットで返します。|
|[endofmonth()](endofmonthfunction.md)|指定した場合、日付を含む月末をオフセットで返します。|
|[endofweek()](endofweekfunction.md)|指定した場合、日付を含む週の終わり (オフセットでシフト) を返します。|
|[endofyear()](endofyearfunction.md)|指定した場合、日付を含む年の終わりを返します。|
|[format_datetime()](format-datetimefunction.md)|フォーマット パターン パラメータに基づいて日時パラメータをフォーマットします。|
|[format_timespan()](format-timespanfunction.md)|書式パターン パラメータに基づいて書式指定時パラメータをフォーマットします。|
|[getmonth()](getmonthfunction.md)|datetime から月 (1 ～ 12) を取得します。|
|[getyear()](getyearfunction.md)|日時引数の年の部分を返します。|
|[hourofday()](hourofdayfunction.md)|指定された日付の時間数を表す整数を返します。|
|[make_datetime()](make-datetimefunction.md)|指定した日時から日付時刻スカラー値を作成します。|
|[make_timespan()](make-timespanfunction.md)|指定した期間からタイムスパン スカラー値を作成します。|
|[monthofyear()](monthofyearfunction.md)|整数を返します指定した年の月数を表します。|
|[Now()](nowfunction.md)|現在の UTC 時刻を返します。|
|[始まりの日()](startofdayfunction.md)|指定した場合、日付を含む日付の開始日をオフセットでシフトして返します。|
|[開始月()](startofmonthfunction.md)|指定した場合、日付を含む月の開始位置をオフセットで返します。|
|[週の始まり()](startofweekfunction.md)|指定した場合、日付を含む週の開始位置を返します。|
|[startofyear()](startofyearfunction.md)|指定した場合、日付を含む年の始まりを返します。|
|[todatetime()](todatetimefunction.md)|入力を日付時刻スカラーに変換します。|
|[totimespan()](totimespanfunction.md)|入力をタイムスパン スカラーに変換します。|
|[weekofyear()](weekofyearfunction.md)|週番号を表す整数を返します。|


## <a name="dynamicarray-functions"></a>動的/配列関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|多数の動的配列を 1 つの配列に連結します。|
|[array_iif()](arrayifffunction.md)|要素に基づく iif 関数を配列に適用します。|
|[array_index_of()](arrayindexoffunction.md)|指定された項目を配列で検索し、その位置を返します。|
|[array_length()](arraylengthfunction.md)|動的配列内の要素の数を計算します。|
|[array_slice()](arrayslicefunction.md)|動的配列のスライスを抽出します。|
|[array_split()](arraysplitfunction.md)|入力配列から分割された配列の配列を作成します。|
|[bag_keys()](bagkeysfunction.md)|動的プロパティ バッグ オブジェクト内のすべてのルート キーを列挙します。|
|[パック()](packfunction.md)|名前と値のリストから動的オブジェクト (プロパティ バッグ) を作成します。|
|[pack_all()](packallfunction.md)|表形式の式のすべての列から動的オブジェクト (プロパティ バッグ) を作成します。|
|[pack_array()](packarrayfunction.md)|すべての入力値を動的配列にパックします。|
|[繰り返し()](repeatfunction.md)|一連の等しい値を保持する動的配列を生成します。|
|[set_difference()](setdifferencefunction.md)|最初の配列にあるが、他の配列に含まれているすべての個別値のセットの配列を返します。|
|[set_has_element()](sethaselementfunction.md)|指定した配列に指定した要素が含まれているかどうかを判断します。|
|[set_intersect()](setintersectfunction.md)|すべての配列内にあるすべての個別値のセットの配列を返します。|
|[set_union()](setunionfunction.md)|指定された配列のいずれかにある、すべての個別の値のセットの配列を返します。|
|[treepath()](treepathfunction.md)|動的オブジェクトのリーフを識別するパス式をすべて列挙します。|
|[ジップ()](zipfunction.md)|zip 関数は、任意の数の動的配列を受け取り、その要素がそれぞれ同じインデックスの入力配列の要素を保持する配列を返します。|


## <a name="window-scalar-functions"></a>ウィンドウスカラー関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[Next()](nextfunction.md)|シリアル化された行セットの場合、オフセットに従って後の行から指定された列の値を返します。|
|[prev()](prevfunction.md)|シリアル化された行セットの場合、オフセットに従って前の行から指定した列の値を返します。|
|[row_cumsum()](rowcumsumfunction.md)|列の累積合計を計算します。|
|[row_number()](rownumberfunction.md)|シリアル化された行セット内の行番号 (指定されたインデックスから始まる連続する番号、または既定では 1 から始まる番号) を返します。|

## <a name="flow-control-functions"></a>フロー制御関数

|関数名            |説明                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|評価された式のスカラー定数値を返します。|

## <a name="mathematical-functions"></a>数学関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|入力の絶対値を計算します。|
|[アコス()](acosfunction.md)|コサインが指定された数 (cos() の逆演算) である角度を返します。|
|[アシン()](asinfunction.md)|指定した数 (sin() の逆演算) を sine に設定した角度を返します。|
|[アタン()](atanfunction.md)|指定した数値 (tan() の逆の操作) をタンジェントが指定した角度を返します。|
|[atan2()](atan2function.md)|原点から点(y,x)までの正の X 軸と光線の間の角度をラジアン単位で計算します。|
|[beta_cdf()](beta-cdffunction.md)|標準の累積ベータ分布関数を返します。|
|[beta_inv()](beta-invfunction.md)|ベータ累積確率 β 密度関数の逆関数を返します。|
|[beta_pdf()](beta-pdffunction.md)|確率密度のベータ関数を返します。|
|[コス()](cosfunction.md)|コサイン関数を返します。|
|[コット()](cotfunction.md)|指定した角度の三角法のコタンジェントをラジアン単位で計算します。|
|[度()](degreesfunction.md)|数式の度 = (180 / PI) * 角度ラジアンを使用して、ラジアンの角度値を度数に変換します。|
|[exp()](exp-function.md)|x の底e指数関数は、e を累乗 x: e^x に引き上げた値です。|
|[exp10()](exp10-function.md)|x の底10指数関数で、10 が累乗 x: 10^x に引き上げられます。|
|[exp2()](exp2-function.md)|x の底2の指数関数で、2 は累乗 x: 2 ^x に上げられます。|
|[ガンマ()](gammafunction.md)|ガンマ関数を計算します。|
|[ハッシュ()](hashfunction.md)|入力値のハッシュ値を返します。|
|[hash_combine()](hash_combinefunction.md)|2 つ以上のハッシュ値を組み合わせます。|
|[hash_many()](hash_manyfunction.md)|複数の値のハッシュ値を組み合わせた値を返します。|
|[isfinite()](isfinitefunction.md)|入力が有限値 (無限でも NaN でもない) かどうかを返します。|
|[isinf()](isinffunction.md)|入力が無限 (正または負) の値かどうかを返します。|
|[isnan()](isnanfunction.md)|入力が数値ではない (NaN) 値かどうかを返します。|
|[ログ()](log-function.md)|自然対数関数を返します。|
|[ログ10()](log10-function.md)|コモン (底 10) 対数関数を返します。|
|[ログ2()](log2-function.md)|2 を底数の対数関数を返します。|
|[loggamma()](loggammafunction.md)|ガンマ関数の絶対値のログを計算します。|
|[ない()](notfunction.md)|bool 引数の値を反転します。|
|[パイ()](pifunction.md)|Pi (π) の定数値を返します。|
|[パウ()](powfunction.md)|パワーに上げた結果を返します。|
|[ラジアン()](radiansfunction.md)|式ラジアン = (PI / 180 ) * 角度を使用して、角度の値をラジアン単位で値に変換します。|
|[Rand()](randfunction.md)|乱数を返します。|
|[範囲()](rangefunction.md)|一連の等間隔の値を保持する動的配列を生成します。|
|[ラウンド()](roundfunction.md)|指定した精度の丸めソースを返します。|
|[記号()](signfunction.md)|数値式の符号。|
|[シン()](sinfunction.md)|正常関数を返します。|
|[sqrt()](sqrtfunction.md)|平方根関数を返します。|
|[タン()](tanfunction.md)|正接関数を返します。|
|[welch_test()](welch-testfunction.md)|[ウェルチ検定関数のp値を](https://en.wikipedia.org/wiki/Welch%27s_t-test)計算します。|


## <a name="metadata-functions"></a>メタデータ関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|列名を文字列と既定値として使用します。 列への参照が存在する場合は、その列への参照を返します。|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|クエリを実行している現在のクラスターを返します。|
|[current_database()](current-database-function.md)|スコープ内のデータベースの名前を返します。|
|[current_principal()](current-principalfunction.md)|このクエリを実行している現在のプリンシパルを返します。|
|[current_principal_details()](current-principal-detailsfunction.md)|クエリを実行しているプリンシパルの詳細を返します。|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|クエリを実行している現在のプリンシパルのグループ メンバーシップまたはプリンシパル ID を確認します。|
|[cursor_after()](cursorafterfunction.md)|カーソルの前の値の後に取り込まれたレコードにアクセスするために使用されます。|
|[estimate_data_size()](estimate-data-sizefunction.md)|表形式の式の選択列の推定データ サイズを返します。|
|[extent_id()](extentidfunction.md)|現在のレコードが存在するデータ シャード ("エクステント") を識別する一意の識別子を返します。|
|[extent_tags()](extenttagsfunction.md)|現在のレコードが存在するデータ シャード ("エクステント") のタグを持つ動的配列を返します。|
|[ingestion_time()](ingestiontimefunction.md)|レコードの$IngestionTime非表示の日時列、または null を取得します。|


## <a name="rounding-functions"></a>丸め関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[ビン()](binfunction.md)|値を切り捨てて、指定された bin サイズの倍数である整数にします。|
|[bin_at()](binatfunction.md)|値を固定サイズの "bin" に切り捨て、ビンの開始点を制御します。 (ビン関数も参照してください。|
|[天井()](ceilingfunction.md)|指定した数値式より大きい、または等しい最小整数を計算します。|
|[フロア()](floorfunction.md)|値を切り捨てて、指定された bin サイズの倍数である整数にします。|


## <a name="conditional-functions"></a>条件関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[ケース()](casefunction.md)|述語のリストを評価し、述部が満たされた最初の結果式を返します。|
|[コアレス()](coalescefunction.md)|式のリストを評価し、最初の非 null (または文字列の場合は空でない) 式を返します。|
|[iif()/iff()](iiffunction.md)|最初の引数 (述語) を評価し、述語が true (2 番目) または false (3 番目) に評価されたかどうかに応じて、2 番目の引数または 3 番目の引数の値を返します。|
|[max_of()](max-offunction.md)|評価された複数の数値式の最大値を返します。|
|[min_of()](min-offunction.md)|評価された複数の数値式の最小値を返します。|

## <a name="series-element-wise-functions"></a>系列要素関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|2 つの数値系列入力の要素方向の加算を計算します。|
|[series_divide()](series-dividefunction.md)|2 つの数値系列入力の要素ごとの分割を計算します。|
|[series_equals()](series-equalsfunction.md)|2 つの数値系列入力の要素に`==`等しい ( ) 論理演算を計算します。|
|[series_greater()](series-greaterfunction.md)|2 つの数値系列入力の要素`>`的に大きな ( ) 論理演算を計算します。|
|[series_greater_equals()](series-greater-equalsfunction.md)|2 つの数値系列入力の要素方向の大`>=`または等しいロジック演算を計算します。|
|[series_less()](series-lessfunction.md)|2 つの数値系列入力の要素`<`的な少ない ( ) ロジック演算を計算します。|
|[series_less_equals()](series-less-equalsfunction.md)|2 つの数値系列入力の要素の小`<=`さまたは等しい ( ) ロジック演算を計算します。|
|[series_multiply()](series-multiplyfunction.md)|2 つの数値系列入力の要素ごとの乗算を計算します。|
|[series_not_equals()](series-not-equalsfunction.md)|2 つの数値系列入力の要素に等`!=`しくない論理演算を計算します。|
|[series_subtract()](series-subtractfunction.md)|2 つの数値系列入力の要素ごとの減算を計算します。|

## <a name="series-processing-functions"></a>シリーズ処理機能

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|系列をコンポーネントに分解します。|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|系列分解に基づいて、系列の異常を検索します。|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|シリーズ分解に基づく予測。|
|[series_fill_backward()](series-fill-backwardfunction.md)|系列内の欠損値の逆方向の埋め込み補間を実行します。|
|[series_fill_const()](series-fill-constfunction.md)|系列内の欠損値を指定した定数値に置き換えます。|
|[series_fill_forward()](series-fill-forwardfunction.md)|系列内の欠損値の順方向フィル補間を実行します。|
|[series_fill_linear()](series-fill-linearfunction.md)|系列内の欠損値の線形補間を実行します。|
|[series_fir()](series-firfunction.md)|シリーズに有限インパルス応答フィルタを適用します。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|2 つのセグメントの線形回帰を 1 つの系列に適用し、複数の列を返します。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|系列に 2 つのセグメントの線形回帰を適用し、動的オブジェクトを返します。|
|[series_fit_line()](series-fit-linefunction.md)|複数の列を返す、系列に線形回帰を適用します。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|系列に線形回帰を適用し、動的オブジェクトを返します。|
|[series_iir()](series-iirfunction.md)|系列に無限インパルス応答フィルタを適用します。|
|[series_outliers()](series-outliersfunction.md)|系列の異常ポイントをスコア付けします。|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|2つの系列のピアソン相関係数を計算します。|
|[series_periods_detect()](series-periods-detectfunction.md)|時系列に存在する最も重要な期間を検索します。|
|[series_periods_validate()](series-periods-validatefunction.md)|時系列に指定された長さの周期パターンが含まれているかどうかをチェックします。|
|[series_seasonal()](series-seasonalfunction.md)|系列の季節成分を検索します。|
|[series_stats()](series-statsfunction.md)|複数の列の系列の統計を返します。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|動的オブジェクトの系列の統計情報を返します。|

## <a name="string-functions"></a>文字列関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|文字列を base64 文字列としてエンコードします。|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|base64 文字列を UTF-8 文字列にデコードします。|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|base64 文字列を長い値の配列にデコードします。|
|[カウントフ()](cotfunction.md)|文字列内の部分文字列の出現回数をカウントします。 プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。|
|[抽出()](extractfunction.md)|テキスト文字列から 正規表現 との一致を取得します。|
|[extract_all()](extractallfunction.md)|テキスト文字列から正規表現のすべての一致を取得します。|
|[extractjson()](extractjsonfunction.md)|パス式を使用している JSON テキストから、指定された要素を取得します。|
|[インデックスの](indexoffunction.md)|Function は、入力文字列内で指定された文字列が最初に出現した場合の 0 から始まるインデックスを報告します。|
|[は空です()](isemptyfunction.md)|引数が空の文字列または null の場合は true を返します。|
|[isnotempty()](isnotemptyfunction.md)|引数が空の文字列でない場合、または null の場合は true を返します。|
|[を返す](isnotnullfunction.md)|引数が null でない場合は true を返します。|
|[を返します。](isnullfunction.md)|その唯一の引数を評価し、引数が null 値に評価されるかどうかを示す bool 値を返します。|
|[parse_csv()](parsecsvfunction.md)|コンマ区切り値を表す指定された文字列を分割し、これらの値を持つ文字列配列を返します。|
|[parse_ipv4()](parse-ipv4function.md)|入力を long (符号付き 64 ビット) 数値表現に変換します。|
|[parse_json()](parsejsonfunction.md)|文字列を JSON 値として解釈し、値を動的として返します。|
|[parse_url()](parseurlfunction.md)|絶対 URL 文字列を解析し、URL のすべての部分を含む動的オブジェクトを返します。|
|[parse_urlquery()](parseurlqueryfunction.md)|url クエリ文字列を解析し、クエリ パラメーターを含む動的オブジェクトを返します。|
|[parse_version()](parse-versionfunction.md)|version の入力文字列形式を比較可能な 10 進数に変換します。|
|[置換()](replacefunction.md)|正規表現のすべての一致を別の文字列に置き換えます。|
|[リバース()](reversefunction.md)|関数は入力文字列を逆にします。|
|[スプリット()](splitfunction.md)|指定した区切り文字に従って指定した文字列を分割し、含まれている部分文字列を含む文字列配列を返します。|
|[ストルキャット()](strcatfunction.md)|1 個から 64 個の引数を連結します。|
|[strcat_delim()](strcat-delimfunction.md)|2 ~ 64 個の引数を区切り文字で連結し、最初の引数として指定します。|
|[strcmp()](strcmpfunction.md)|2 つの文字列を比較します。|
|[ストレン()](strlenfunction.md)|入力文字列の長さを文字数で返します。|
|[strrep()](strrepfunction.md)|指定された文字列の指定された回数を繰り返します (デフォルト - 1)。|
|[サブストリング()](substringfunction.md)|文字列のインデックスから末尾までのインデックスから始まる部分文字列を抽出します。|
|[トッパー()](toupperfunction.md)|文字列を大文字に変換します。|
|[翻訳()](translatefunction.md)|指定された文字列内の文字のセット ('検索リスト') を別の文字セット ('置換リスト') に置き換えます。|
|[トリム()](trimfunction.md)|指定した正規表現の先頭と末尾の一致をすべて削除します。|
|[trim_end()](trimendfunction.md)|指定した正規表現の末尾一致を削除します。|
|[trim_start()](trimstartfunction.md)|指定した正規表現の先頭に一致する文字列を削除します。|
|[url_decode()](urldecodefunction.md)|この関数は、エンコードされた URL を a から通常の URL 表現に変換します。|
|[url_encode()](urlencodefunction.md)|この関数は、入力 URL の文字をインターネット上で転送できる形式に変換します。|

## <a name="ip-v4-functions"></a>IP v4 機能

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|2 つの IPv4 文字列を比較します。|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|2 つの IPv4 文字列に一致します。|
|[parse_ipv4()](parse-ipv4function.md)|入力文字列を long (符号付き 64 ビット) 数値表現に変換します。|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|入力文字列と IP プレフィックス マスクを long (符号付き 64 ビット) 数値表現に変換します。|

## <a name="type-functions"></a>型関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|単一引数の実行時の型を返します。|


## <a name="scalar-aggregation-functions"></a>スカラー集計関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|hll の結果から dcount を計算します (hll または hll マージによって生成されました)。|
|[hll_merge()](hllmergefunction.md)|hll の結果 (集約バージョン hll-merge() のスカラーバージョン) をマージします。|
|[percentile_tdigest()](percentile-tdigestfunction.md)|消化器の結果 (消化不良またはマージ -消化によって生成された) から百分位数の結果を計算します。|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|データセット内の値のランク付け率を計算します。|
|[rank_tdigest()](rank-tdigest.md)|セット内の値の相対ランクを計算します。|
|[tdigest_merge()](tdigest-mergefunction.md)|tdigest の結果 (集計バージョンのスカラーバージョンの tdigest-merge()) をマージします。|

## <a name="geo-spatial-functions"></a>地理空間関数

|関数名|説明|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|地球上の 2 つの地理空間座標間の最短距離を計算します。|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|ジオハッシュ四角形領域の中心を表す地理空間座標を計算します。|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|空間座標が地球上の円の内側にあるかどうかを計算します。|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|地理的位置のジオハッシュ文字列値を計算します。|