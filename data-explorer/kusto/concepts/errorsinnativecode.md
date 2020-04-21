---
title: ネイティブ コードのエラー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのネイティブ コードのエラーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/12/2019
ms.openlocfilehash: fa36bec3586a5e316b08ebc2949617268f0270ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523258"
---
# <a name="errors-in-native-code"></a>ネイティブ コードのエラー

Kusto エンジンはネイティブ コードで記述され、負`HRESULT`の値を使用してエラーを報告します。 これらのエラーは、通常はプログラム API では見られませんが、発生する可能性があります。 たとえば、操作の失敗は`Exception from HRESULT:`*、"HRESULT-CODE"* の状態を示す場合があります。

Kusto ネイティブ エラー コードは`MAKE-HRESULT`、次の Windows マクロを使用して定義されます。

* 重大度の設定`1`
* 施設の設定`0xDA`
  
次のエラー コードが定義されています。

|マニフェスト定数                  |コード |値        |意味                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|データ シャードを読み込めませんでした                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|クエリの実行は、許可されたリソースを超えたため中止されました                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|データ ストリームは不適切な形式であるため解析できません                                                      |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|このクエリの結果セットが、許容されるレコード/サイズの制限を超えています                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|バージョンが不明なため、結果ストリームを解析できません                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|キー/値データベース操作の実行に失敗しました                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|クエリはキャンセルされました                                                                                            |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|使用可能なプロセス メモリが不足しているため、操作は中止されました                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|取り込みのために送信された CSV ドキュメントに、フィールド数が間違っているレコードがあります (他のレコードに対して相対的)。|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|取り込みのために提出されたドキュメントが、許容長を超えています                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|要求されたエンティティが見つかりませんでした                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|取り込みのために送信された csv ドキュメントに、見積もりが欠落しているフィールドがあります                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|算術オーバーフロー エラーを表します (計算の結果が変換先の型に対して大きすぎます)    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|ブロックされた行ストア キーにアクセスしようとしました                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|行ストアがシャットダウン中です                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|行ストアの記憶域に割り当てられたディスク領域がいっぱいです                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|行ストアストレージからの読み取りに失敗しました                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|行ストア ストレージから値を取得できない                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|行ストアを初期化中です                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|行ストアへの値の挿入が調整されました                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|行ストアが読み取り専用状態で接続されている                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|行ストアは現在使用できません                                                                             |