---
title: 評価プラグイン演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのプラグイン演算子の評価について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: dc7e410b79ad73c1ba9fa807142177f4270b7dfa
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803116"
---
# <a name="evaluate-operator-plugins"></a>evaluate 演算子プラグイン

サービス側のクエリ拡張機能 (プラグイン) を呼び出します。

`evaluate`演算子は、**プラグイン**と呼ばれるクエリ言語拡張機能を呼び出すことができる表形式演算子です。 プラグインは、有効または無効にすることができます (常に使用可能な他の言語コンストラクトとは異なります)。また、言語のリレーショナルな性質によって "バインド" されません (たとえば、事前に定義された、静的に決定された出力スキーマがない可能性があります)。

> [!NOTE]
> * 構文的には、 `evaluate` 表形式関数を呼び出す[invoke 演算子](./invokeoperator.md)と同様の動作をします。
> * 評価演算子によって指定されたプラグインは、クエリ実行または引数の評価の通常の規則ではバインドされていません。
> * 特定のプラグインには、特定の制限がある場合があります。 たとえば、データに依存する出力スキーマを持つプラグイン ( [bag_unpack プラグイン](./bag-unpackplugin.md)や[ピボットプラグイン](./pivotplugin.md)など) は、クラスター間クエリを実行するときには使用できません。

## <a name="syntax"></a>構文 

[*T* `|` ] `evaluate` [ *evaluateparameters* ] *pluginname* `(` [*PluginArg1* [ `,` *PluginArg2*]...`)`

## <a name="arguments"></a>引数

* *T*は、プラグインに対する省略可能な表形式の入力です。 (一部のプラグインは入力を受け取らず、表形式のデータソースとして機能します)。
* *Pluginname*は、呼び出されるプラグインの必須の名前です。
* *PluginArg1*、...は、プラグインに対する省略可能な引数です。
* *evaluateparameters*: *Name* `=` 評価操作と実行プランの動作を制御する名前*値*の形式で、0個以上の (スペースで区切られた) パラメーターです。 各プラグインでは、各パラメーターの処理方法が異なる場合があります。 具体的な動作については、各プラグインのドキュメントを参照してください。  

## <a name="parameters"></a>パラメーター

サポートされているパラメーターは次のとおりです。 

  |名前                |値                           |説明                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [配布ヒント](#distribution-hints) |
  |`hint.pass_filters` |`true`, `false`| `evaluate`オペレーターがプラグインの前に一致するフィルターをパススルーすることを許可します。 フィルターは、演算子の前に存在する列を参照している場合、' 一致する ' と見なされ `evaluate` ます。 既定値: `false` |
  |`hint.pass_filters_column` |*column_name*| プラグインオペレーターが、プラグインの前に*column_name*を参照するフィルターをパススルーできるようにします。 パラメーターは、異なる列名を使用して複数回使用できます。 |

## <a name="distribution-hints"></a>配布ヒント

配布ヒントでは、プラグインの実行を複数のクラスターノードに分散する方法を指定します。 各プラグインは、ディストリビューションに対して異なるサポートを実装する場合があります。 プラグインのドキュメントでは、プラグインによってサポートされるディストリビューションオプションを指定します。

指定できる値

* `single`: プラグインの1つのインスタンスがクエリデータ全体で実行されます。
* `per_node`: プラグイン呼び出しの前のクエリが複数のノードに分散されている場合、プラグインのインスタンスは、含まれているデータを介して各ノードで実行されます。
* `per_shard`: プラグイン呼び出し前のデータがシャードに分散されている場合、プラグインのインスタンスはデータの各シャードで実行されます。
