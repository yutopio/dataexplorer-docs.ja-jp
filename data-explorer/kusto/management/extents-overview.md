---
title: エクステント (data シャード)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのエクステント (data シャード) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
ms.openlocfilehash: 839042b50fce6409de29c4cf979a253a7485fb7f
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977152"
---
# <a name="extents-data-shards"></a>エクステント (データシャード)

## <a name="overview"></a>概要

Kusto は、膨大な数のレコード (行) と大量のデータを含むテーブルをサポートするように構築されています。 このような大きなテーブルを処理できるようにするために、Kusto は、各テーブルのデータを、**データシャード**または**エクステント**(2 つの用語はシノニム) と呼ばれる小さな "タブレット" に分割します。これにより、すべてのテーブルのエクステントの和集合にテーブルのデータが保持されます。 個々のエクステントは、1つのノードの容量よりも小さく保持され、エクステントはクラスターのノードに分散され、スケールアウトを実現します。 

1つは、範囲をミニテーブルの一種と考えることができます。 このエクステントは、メタデータ (エクステント内のデータのスキーマと、エクステント内のデータに関連付けられているオプションのタグなどの追加情報) とデータを保持します。 また、このエクステントは通常、Kusto がデータを効率的に照会できるようにする情報を保持します。たとえば、エクステント内のデータ列ごとにインデックスを作成したり、列データがエンコードされている場合はエンコーディング辞書を使用したりできます。 したがって、テーブルのデータは、テーブルのエクステント内のすべてのデータを結合したものになります。

エクステントは*変更*できません。 作成されたエクステントは変更されません。エクステントのクエリ、別のノードへの再割り当て、またはテーブルからの削除が可能になります。 データの変更は、1つまたは複数の新しいエクステントを作成し、古いエクステントと新しいエクステントをトランザクションでスワップすることによって行われます。

エクステントは、物理的に列に配置されたレコードのコレクションを保持します。
この手法 (**単票形式のストア**) を使用すると、データを効率的にエンコードおよび圧縮できます (同じ列の異なる値が頻繁に "相互に似ている" ことが多いため)。また、クエリで使用される列だけを読み込む必要があるため、大量のデータのクエリを効率的に行うことができます。 内部的には、エクステント内のデータの各列がセグメントに分割され、セグメントがブロックに分割されます。 この分割 (クエリでは観測不可能) により、Kusto は列の圧縮とインデックス作成を最適化できます。

クエリの効率を維持するために、小さなエクステントはより大きなエクステントにマージされます。
これは、構成済みの[マージポリシー](mergepolicy.md)と[シャーディングポリシー](shardingpolicy.md)に従って、kusto によってバックグラウンドプロセスとして自動的に実行されます。
エクステントをまとめてマージすると、多数のエクステントを追跡するための管理オーバーヘッドが軽減されますが、さらに重要なのは、Kusto でインデックスを最適化し、圧縮を向上させることができることです。 エクステントのマージは、エクステントが特定の制限 (サイズなど) に達すると停止します。これは、特定のポイントをマージすると、効率が向上するだけでなく、

データの[パーティション分割ポリシー](partitioningpolicy.md)がテーブルで定義されている場合、エクステントは作成後に別のバックグラウンドプロセスを実行します (インジェスト後)。 このプロセスでは、ソースエクステントからデータを再取り込みし、*同*一のエクステントを作成します。この場合、テーブルの*パーティションキー*である列の値がすべて同じパーティションに属しています。 ポリシーに*ハッシュパーティションキー*が含まれている場合は、同じパーティションに属するすべての同種エクステントがクラスター内の同じデータノードに割り当てられていることが保証されます。

> [!NOTE]
> 範囲レベルの操作 (マージ、エクステントタグの変更など) は、既存のエクステントを変更しません。
> 代わりに、既存のソースエクステントに基づいてこれらの操作に新しいエクステントが作成されます。その後、これらの新しいエクステントは、1つのトランザクションで forefathers を置き換えます。

このため、次のような範囲の共通の "ライフサイクル" があります。

1. このエクステントは**インジェスト**操作によって作成されます。
2. エクステントは他のエクステントとマージされます。 マージするエクステントが小さい場合、Kusto は実際にそれらのエクステントにインジェストプロセスを実行します (**再構築**と呼ばれます)。 エクステントが特定のサイズに達すると、マージはインデックスに対してのみ実行され、エクステントのデータアーティファクトは変更されません。
3. マージされたエクステント (場合によっては、他のマージされたエクステントに対する系列を追跡するもの) は、保持ポリシーによって削除されます。 時間 (過去 x 時間/日) に基づいてエクステントが削除されると、マージされた1つの範囲内の最新のエクステントの作成日が計算に取り込まれます。

## <a name="extent-ingestion-time"></a>エクステントインジェスト時間

各エクステントの重要な情報の1つに、インジェスト時間があります。 この時間は、Kusto によって次のように使用されます。

1. リテンション期間 (以前に取り込まれたされていたエクステントは、前に削除されます)。
2. キャッシュ (最近取り込まれたされたエクステントは暑すぎるかになります)。
3. サンプリング (など`take`のクエリ操作を使用する場合、最近のエクステントが優先されます)。

実際には、Kusto は`datetime`エクステントごとに 2 `MinCreatedOn`つ`MaxCreatedOn`の値 (と) を追跡します。
これらの値は同じように開始されますが、エクステントが他のエクステントとマージされると、結果として得られるエクステントの値は、マージされたすべてのエクステントの最小値と最大値になります。

エクステントのインジェスト時間は、次の3つの方法のいずれかで設定できます。

1. 通常、インジェストを実行するノードは、ローカルクロックに応じてこの値を設定します。
2. **インジェスト時間ポリシー**がテーブルに設定されている場合、インジェストを実行するノードは、クラスターの管理ノードのローカルクロックに応じてこの値を設定します。これにより、後のすべての ingestions で取り込み時間の値が高くなることが保証されます。
3. クライアントは、この時間を設定できます。 (これは、たとえば、クライアントがデータを再取り込む必要があり、再取り込まれたデータが到着したかのように表示されないようにする場合などに便利です。    

## <a name="extent-tagging"></a>エクステントのタグ付け

Kusto は、エクステントと共に格納されるメタデータの一部として、複数のオプションの*エクステントタグ*をエクステントにアタッチすることをサポートしています。 エクステントタグ (または単に*タグ*) は、エクステントに関連付けられている文字列です。 エクステントに関連付けられたタグを表示するには、[[エクステントの表示]](extents-commands.md#show-extents)コマンドを使用できます。また、エクステント内のレコードに関連付けられているタグを表示するには、[エクステント-tags ()](../query/extenttagsfunction.md)関数を使用します。
エクステントタグを使用すると、エクステント内のすべてのデータに関連するプロパティを効率的に記述できます。
たとえば、インジェスト中にエクステントタグを追加して、取り込まれたされるデータのソースを示し、後でそのタグを使用することができます。 データを記述するときに、2つ以上のエクステントがマージされると、関連付けられたタグもマージされます。これにより、結果として得られるエクステントのタグが、マージされるエクステントのすべてのエクステントタグの和集合になります。

Kusto は、値が形式*プレフィックス**サフィックス*を持つすべてのエクステントタグに特別な意味を割り当てます。 *prefix*は次のいずれかになります。

* `drop-by:`
* `ingest-by:`

### <a name="drop-by-extent-tags"></a>' drop: ' エクステントタグ

プレフィックスで始まるタグを**`drop-by:`** 使用すると、他のどのエクステントをマージするかを制御できます。特定`drop-by:`のタグを持つエクステントは結合できますが、他のエクステントとはマージされません。 これにより、ユーザーは、次のコマンドのように、 `drop-by:`タグに従ってエクステントを削除するコマンドを発行できます。

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

#### <a name="performance-notes"></a>パフォーマンスに関する注意

* オーバータグは`drop-by`使用しないことをお勧めします。 上記の方法でデータを削除するためのサポートは、めったに発生しないイベントを対象としており、レコードレベルのデータを置換するためのものではなく、このようにタグ付けされているデータが "大きい" という事実に非常に依存しています。 レコードごとに別のタグを指定しようとすると、レコード数が少ないと、パフォーマンスに重大な影響が生じる可能性があります。
* データの取り込まれた後に、このようなタグが必要ない場合は、[タグを削除](extents-commands.md#drop-extent-tags)することをお勧めします。

### <a name="ingest-by-extent-tags"></a>' インジェスト: ' エクステントタグ

**`ingest-by:`** プレフィックスで始まるタグは、データが一度だけ取り込まれたされるようにするために使用できます。 ユーザーは、 `ingest-by:` **`ingestIfNotExists`** プロパティを使用して、この特定のタグを持つエクステントが既に存在する場合に、データが取り込まれたされないようにするインジェストコマンドを発行できます。
と`tags` `ingestIfNotExists`の両方の値は、JSON としてシリアル化された文字列の配列です。

次の例では、データを1回だけ取り込みします (2 番目と3番目のコマンドは何も行いません)。

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> 一般的な場合、取り込みコマンドには`ingest-by:`タグと`ingestIfNotExists`プロパティの両方が含まれ、同じ値に設定されます (上記の3番目のコマンドを参照)。

#### <a name="performance-notes"></a>パフォーマンスに関する注意

- 乱用`ingest-by`タグは推奨されません。
Kusto が供給されているパイプラインにデータ重複があることがわかっている場合は、データを Kusto に取り込みする前に可能な`ingest-by`限りこれらを解決し、kusto に取り込みする部分で重複が発生する可能性がある場合にのみ kusto にタグを使用することをお勧めします。 インジェスト呼び出しごとに一意`ingest-by`のタグを設定しようとすると、パフォーマンスに重大な影響が生じる可能性があります。
- データの取り込まれた後に、このようなタグが必要ない場合は、[タグを削除](extents-commands.md#drop-extent-tags)することをお勧めします。