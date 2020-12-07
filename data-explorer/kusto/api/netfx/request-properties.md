---
title: 要求プロパティと ClientRequestProperties-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの要求プロパティと ClientRequestProperties について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: a7474dc04e85f439fc611c44213694041fedc1a4
ms.sourcegitcommit: f134d51e52504d3ca722bdf6d33baee05118173a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563293"
---
# <a name="request-properties-and-clientrequestproperties"></a>要求プロパティと ClientRequestProperties

.NET SDK を介して Kusto から要求が行われた場合は、次の情報を指定します。

* 接続先のサービスエンドポイント、認証パラメーター、および同様の接続関連情報を示す接続文字列。 プログラムでは、接続文字列はクラスによって表され `KustoConnectionStringBuilder` ます。
* 要求の "スコープ" を記述するために使用されるデータベースの名前。
* 要求のテキスト (クエリまたはコマンド)。
* クライアントからサービスに提供され、要求に適用される追加のプロパティ。 プログラムでは、これらのプロパティはというクラスによって保持され [`ClientRequestProperties`](#clientrequestproperties) ます。

## <a name="clientrequestproperties"></a>ClientRequestProperties

クライアント要求のプロパティは、要求に適用される制限およびポリシーに影響を与える可能性があります。

クラスは、 `Kusto.Data.Common.ClientRequestProperties` 次の3種類のデータを保持します。

* [名前付きプロパティ](#named-properties) -これらのプロパティは、デバッグを容易にします。 たとえば、プロパティは、クライアントとサービスの対話を追跡するために使用される関連付け文字列を提供する場合があります。 
* [Clientrequestproperties オプション](#clientrequestproperties-options) -オプション名からオプション値へのマッピング。
* [クエリパラメーター](../../query/queryparametersstatement.md)  -クエリパラメーター名からクエリパラメーター値へのマッピング。 これらのパラメーターを使用すると、クライアントアプリケーションはユーザー入力に基づいて Kusto クエリをパラメーター化できます。

## <a name="named-properties"></a>名前付きプロパティ

> [!NOTE]
> 一部の名前付きプロパティは、"使用しない" とマークされています。 このようなプロパティをクライアントで指定することはできません。また、サービスに影響を与えることもありません。

### <a name="clientrequestid-x-ms-client-request-id"></a>ClientRequestId (x-y-client-request-id)

この名前付きプロパティには、クライアントが指定した要求の id が含まれます。 クライアントは、送信する要求ごとに一意の一意の要求ごとの値を指定する必要があります。 この値を指定すると、デバッグエラーが簡単になり、クエリのキャンセルなど、一部のシナリオで必要になります。

プロパティのプログラム名は `ClientRequestId` であり、HTTP ヘッダーに変換され `x-ms-client-request-id` ます。

クライアントが値を指定していない場合、このプロパティは SDK によって (ランダム) 値に設定されます。

このプロパティの内容は、GUID など、任意の印刷可能な一意の文字列にすることができます。
ただし、クライアントでは次のものを使用することをお勧めします。 *ApplicationName* `.` *ActivityName* `;` *UniqueId*

* *ApplicationName* は、要求を行うクライアントアプリケーションを識別します。
* *ActivityName* は、クライアントアプリケーションがクライアント要求を発行するアクティビティの種類を識別します。
* *UniqueId* は、特定の要求を識別します。

### <a name="application-x-ms-app"></a>アプリケーション (x-y)

Application (x.y-app) という名前のプロパティには、要求を行うクライアントアプリケーションの名前があり、トレースに使用されます。

このプロパティのプログラム名はであり、 `Application` HTTP ヘッダーに変換され `x-ms-app` ます。 この値は、Kusto 接続文字列でとして指定でき `Application Name for Tracing` ます。

このプロパティは、クライアントが独自の値を指定していない場合に、SDK をホストするプロセスの名前に設定されます。

### <a name="user-x-ms-user"></a>ユーザー (x-ms-ユーザー)

ユーザー (x-y-user) の名前付きプロパティには、要求を行うユーザーの id があり、トレースに使用されます。

このプロパティのプログラム名はであり、 `User` HTTP ヘッダーに変換され `x-ms-user` ます。 この値は、Kusto 接続文字列でとして指定でき `User Name for Tracing` ます。

## <a name="use-request-properties"></a>要求のプロパティを使用する

要求のプロパティを制御し、クエリのパラメーター化の値を指定するには、次の手順に従います。 

### <a name="control-request-properties-using-the-rest-api"></a>REST API を使用した要求プロパティの制御

Kusto サービスに対して HTTP 要求を発行する場合は、 `properties` 要求のプロパティを指定するために、POST 要求の本文である JSON ドキュメント内のスロットを使用します。 

> [!NOTE]
> 一部のプロパティ (クライアントが要求を識別するためにサービスに提供する関連付け ID である "クライアント要求 ID" など) は、HTTP ヘッダーで指定できます。また、HTTP GET が使用されている場合にも設定できます。
詳細については、「 [Kusto REST API request オブジェクト](../rest/request.md)」を参照してください。

### <a name="provide-values-for-query-parameterization-as-request-properties"></a>クエリのパラメーター化の値を要求プロパティとして指定する

Kusto クエリでは、クエリテキスト内の特殊な [declare クエリパラメーター](../../query/queryparametersstatement.md) ステートメントを使用してクエリパラメーターを参照できます。 このステートメントを使用すると、クライアントアプリケーションは、ユーザー入力に基づいて、セキュリティで保護された方法で Kusto クエリをパラメーター化し、インジェクション攻撃を防ぐことができます。

プログラムによって、、、の各メソッドを使用してプロパティの値を設定し `ClearParameter` `SetParameter` `HasParameter` ます。

REST API では、クエリパラメーターは、他の要求プロパティと同じ JSON エンコード文字列で表示されます。

## <a name="example"></a>例

次の例は、要求プロパティを使用するためのサンプルクライアントコードを示しています。

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="clientrequestproperties-options"></a>ClientRequestProperties オプション

<!-- The following is auto-generated by running  Kusto.Cli.exe -execute:"#crp -doc"           -->
<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `deferpartialqueryfailures` (*OptionDeferPartialQueryFailures*): True の場合、結果セットの一部として部分的なクエリエラーの報告を無効にします。 演算
* `materialized_view_shuffle` (*OptionMaterializedViewShuffleQuery*): クエリで参照されている具体化されたビューにシャッフル戦略を使用するヒント。
プロパティは、具体化されたビュー名の配列であり、使用するシャッフルキーの配列です。
例: ' 動的 ([{"Name": "V1", "Keys": ["K1", "K2"]) ' (シャッフルビュー V1 by K1, K2) または ' 動的 ([{"Name": "V1"}]) ' (すべてのキーによるシャッフルビュー V1) [動的]
* `max_memory_consumption_per_query_per_node` (*OptionMaxMemoryConsumptionPerQueryPerNode*): クエリ全体がノードごとに割り当てることができる既定の最大メモリ量を上書きします。 UInt64
* `maxmemoryconsumptionperiterator` (*OptionMaxMemoryConsumptionPerIterator*): クエリ演算子が割り当てることができるメモリの既定の最大量を上書きします。 UInt64
* `maxoutputcolumns` (*Optionmaxoutputcolumns*): クエリが生成できる列の既定の最大数を上書きします。 長い
* `norequesttimeout` (*OptionNoRequestTimeout*): 要求タイムアウトを最大値に設定できるようにします。 演算
* `notruncation` (*オプション No切り捨て*): 呼び出し元に返されたクエリ結果の切り捨ての抑制を有効にします。 演算
* `push_selection_through_aggregation` (*オプション Pushselection: 集計*): True の場合、集計による単純な選択のプッシュ [Boolean]
* `query_bin_auto_at` (*Querybinautoat*): bin_auto () 関数を評価するときに使用する開始値。 [LiteralExpression]
* `query_bin_auto_size` (*Querybinautosize*): bin_auto () 関数を評価するときに使用する bin サイズ値。 [LiteralExpression]
* `query_cursor_after_default` (*Optionqueryカーソル Afterdefault*): パラメーターを指定せずに呼び出された場合の cursor_after () 関数の既定のパラメーター値。 [string]
* `query_cursor_before_or_at_default` (*Optionqueryカーソル Beforeoratdefault*): パラメーターを指定せずに呼び出された場合の cursor_before_or_at () 関数の既定のパラメーター値。 [string]
* `query_cursor_current` (*Optionquerycursor current*): cursor_current () 関数または current_cursor () 関数によって返されるカーソル値をオーバーライドします。 [string]
* `query_cursor_disabled` (*Optionquerycursor disabled*): クエリのコンテキストでカーソル関数の使用を無効にします。 演算
* `query_cursor_scoped_tables` (*OptionQueryCursorScopedTables*): cursor_after_default するようにスコープを設定するテーブル名の一覧です。 cursor_before_or_at_default (上限は省略可能です)。 動的
* `query_datascope` (*Optionquerydatascope* よる): クエリのデータスコープを制御します。クエリがすべてのデータに適用されるか、一部だけに適用されるかを指定します。 [' default '、' all '、または ' ホットキャッシュ ']
* `query_datetimescope_column` (*Optionquerydatetimescopecolumn*): クエリの datetime スコープ (query_datetimescope_to/query_datetimescope_from) の列名を制御します。 文字列
* `query_datetimescope_from` (*Optionquerydatetimescopefrom*: クエリの datetime スコープ (最も古い) を制御します。 query_datetimescope_column でのみ自動適用されるフィルターとして使用されます (定義されている場合)。 [DateTime]
* `query_datetimescope_to` (*Optionquerydatetimescopeto*: クエリの datetime スコープ (最新) を制御します。 query_datetimescope_column でのみ自動適用されるフィルターとして使用されます (定義されている場合)。 [DateTime]
* `query_distribution_nodes_span` (*OptionQueryDistributionNodesSpanSize*): 設定されている場合、サブクエリのマージの動作方法を制御します。実行中のノードは、ノードのサブグループごとに追加のレベルをクエリ階層に導入します。サブグループのサイズは、このオプションによって設定されます。 通り
* `query_fanout_nodes_percent` (*OptionQueryFanoutNodesPercent*): 実行をファンアウトするノードの割合。 通り
* `query_fanout_threads_percent` (*OptionQueryFanoutThreadsPercent*): 実行をファンアウトするスレッドの割合。 通り
* `query_force_row_level_security` (*OptionQueryForceRowLevelSecurity*): 指定されている場合、row_level_security ポリシーが無効になっている場合でも、規則を強制的に行レベルセキュリティします [ブール値]
* `query_language` (*OptionQueryLanguage*): クエリテキストがどのように解釈されるかを制御します。 [' csl '、' kql '、または ' sql ']
* `query_max_entities_in_union` (*OptionMaxEntitiesToUnion*): クエリによって生成される列の既定の最大数を上書きします。 長い
* `query_now` (*オプション Querynow*): now (0) 関数によって返される datetime 値をオーバーライドします。 [DateTime]
* `query_python_debug` (*Optiondebugpython*): 設定されている場合は、列挙された python ノードの python デバッグクエリを生成します (既定では最初)。 [ブール値または Int]
* `query_results_apply_getschema` (*オプション Queryresults Applygetschema*): 設定されている場合、は、データ自体ではなく、クエリの結果に含まれる各表形式データのスキーマを取得します。 演算
* `query_results_cache_max_age` (*OptionQueryResultsCacheMaxAge*): 正の場合、キャッシュされたクエリ結果の最大有効期間を制御します。 Kusto は [TimeSpan] を返すことができます。
* `query_results_progressive_row_count` (*OptionProgressiveQueryMinRowCountPerUpdate*): 各更新で送信するレコードの数に対する Kusto のヒント (OptionResultsProgressiveEnabled が設定されている場合にのみ有効になります)
* `query_results_progressive_update_period` (*OptionProgressiveProgressReportPeriod*): 進行状況フレームを送信する頻度に対する Kusto としてのヒント (OptionResultsProgressiveEnabled が設定されている場合にのみ有効になります)
* `query_take_max_records` (*OptionTakeMaxRecords*): クエリ結果をこの数のレコードに制限できます。 長い
* `queryconsistency` (*オプション query一貫性*): クエリの一貫性を制御します。 [' strongconsistency ' または ' normalconsistency ' または ' 整合性 ']
* `request_block_row_level_security` (*Optionrequestblockrowlevelsecurity*): 指定されている場合、row_level_security ポリシーが有効になっているテーブルへのアクセスをブロックします [ブール]
* `request_callout_disabled` (*OptionRequestCalloutDisabled*): 指定した場合、要求がユーザー指定のサービスに対してを呼び出すことができないことを示します。 演算
* `request_description` (*Optionrequestdescription*): 要求の作成者が要求の説明として含める任意のテキスト。 文字列
* `request_external_table_disabled` (*Optionrequestexternaltabledisabled*): 指定されている場合、要求が externaltable 内のコードを呼び出すことができないことを示します。 演算
* `request_impersonation_disabled` (*OptionDoNotImpersonate*): 指定されている場合、サービスが呼び出し元の id を偽装してはならないことを示します。 演算
* `request_readonly` (*Optionrequestreadonly*): 指定されている場合、要求は何も書き込めないことを示します。 演算
* `request_remote_entities_disabled` (*OptionRequestRemoteEntitiesDisabled*): 指定した場合、要求がリモートデータベースおよびクラスターにアクセスできないことを示します。 演算
* `request_sandboxed_execution_disabled` (*OptionRequestSandboxedExecutionDisabled*): 指定されている場合、要求がサンドボックス内のコードを呼び出すことができないことを示します。 演算
* `results_progressive_enabled` (*OptionResultsProgressiveEnabled*): 設定すると、プログレッシブクエリストリームが有効になります。
* `servertimeout` (*Optionservertimeout*): 既定の要求タイムアウトをオーバーライドします。 TimeSpan
* `truncationmaxrecords` (*OptionTruncationMaxRecords*): クエリが呼び出し元 (切り捨て) に戻ることが許可されているレコードの既定の最大数を上書きします。 長い
* `truncationmaxsize` (*OptionTruncationMaxSize*): クエリが呼び出し元 (切り捨て) に戻ることが許可されている既定の最大データサイズを上書きします。 長い
* `validate_permissions` (*オプション Validatepermissions*): クエリを実行するためのユーザーの権限を検証します。クエリ自体は実行されません。 演算