---
title: クエリパラメーター宣言ステートメント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのクエリパラメーターの宣言ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d9c5e3b41eef933477981945b22b3aceef958dd8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251658"
---
# <a name="query-parameters-declaration-statement"></a>クエリパラメーター宣言ステートメント

::: zone pivot="azuredataexplorer"

Kusto に送信されるクエリには、一連の名前または値のペアが含まれる場合があります。 これらのペアは、クエリテキスト自体と共に *クエリパラメーター*と呼ばれます。 クエリでは、 *クエリパラメーターの宣言ステートメント*で、名前と型を指定することで、1つ以上の値を参照できます。

クエリパラメーターには、主に次の2つの用途があります。

* インジェクション攻撃に対する保護メカニズムとして。
* クエリをパラメーター化する方法。

特に、ユーザーが入力した入力をクエリで組み合わせて Kusto に送信するクライアントアプリケーションは、このメカニズムを使用して、Kusto と同等の [SQL インジェクション](https://en.wikipedia.org/wiki/SQL_injection) 攻撃から保護する必要があります。

## <a name="declaring-query-parameters"></a>クエリパラメーターの宣言

クエリパラメーターを参照するには、クエリテキスト、または使用する関数が、最初に使用するクエリパラメーターを宣言する必要があります。 パラメーターごとに、宣言によって名前とスカラー型が提供されます。 必要に応じて、パラメーターに既定値を指定することもできます。 既定値は、要求がパラメーターの具体的な値を提供しない場合に使用されます。 次に、その型の通常の解析規則に従って、クエリパラメーターの値を解析します。

## <a name="syntax"></a>構文

`declare``query_parameters` `(` *Name1* `:` *Type1* [ `=` *DefaultValue1*] [ `,` ...]`);`

* *Name1*: クエリで使用されるクエリパラメーターの名前。
* *Type1*: 対応する型 (やなど `string` ) `datetime` 。
  ユーザーによって指定された値は文字列としてエンコードされます。 Kusto は、適切な解析メソッドをクエリパラメーターに適用して、厳密に型指定された値を取得します。
* *DefaultValue1*: パラメーターの省略可能な既定値。 この値は、適切なスカラー型のリテラルである必要があります。

> [!NOTE]
> [ユーザー定義関数](functions/user-defined-functions.md)と同様に、型のクエリパラメーターに既定値を指定する `dynamic` ことはできません。

## <a name="examples"></a>例

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>クライアントアプリケーションでのクエリパラメーターの指定

クエリパラメーターの名前と値は、クエリを作成するアプリケーションによって値として指定され `string` ます。 名前を繰り返すことはできません。

値の解釈は、クエリパラメーターの宣言ステートメントに従って行われます。 すべての値は、クエリの本体のリテラルであるかのように解析されます。 解析は、クエリパラメーター宣言ステートメントによって指定された型に従って行われます。

### <a name="rest-api"></a>REST API

クエリパラメーターは、 `properties` という入れ子になったプロパティバッグで、要求本文の JSON オブジェクトのスロットを通じてクライアントアプリケーションによって提供され `Parameters` ます。 たとえば、ユーザーの誕生日をアプリケーションに要求することによって、あるユーザーの年齢を計算する Kusto への REST API 呼び出しの本文を次に示します。

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Kusto .NET クライアントライブラリを使用するときにクエリパラメーターの名前と値を指定するために、オブジェクトの新しいインスタンスを作成し、、、の各メソッドを使用して `ClientRequestProperties` `HasParameter` `SetParameter` `ClearParameter` クエリパラメーターを操作します。 このクラスは、に対して厳密に型指定された複数のオーバーロードを提供 `SetParameter` します。内部的には、クエリ言語の適切なリテラルを生成し、前述のように REST API を通じてとして送信し `string` ます。 クエリテキスト自体は、 [クエリパラメーターを宣言](#declaring-query-parameters)する必要があります。

### <a name="kustoexplorer"></a>Kusto.Explorer

サービスへの要求を行うときに送信されるクエリパラメーターを設定するには、**クエリパラメーター** "レンチ" アイコン () を使用し `ALT`  +  `P` ます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
