重複を許さないコレクションの場合、配列に `DISTINCT()` するよりも
`HashSet(Of T)` を使うほうが高速になる。

HashSet(Of T) は、VB.NETやC#などの.NET言語で提供されているジェネリックなコレクションクラスの一つです。HashSet(Of T) は、ハッシュセットとして知られるデータ構造を実装しており、一意の要素のコレクションを格納します。これは、重複を許さず、要素の追加、削除、検索などの操作を高速に行うことができます。

以下は、HashSet(Of T) の主な特徴と使い方です：

一意性: HashSet(Of T) は、重複する要素を格納しません。要素の一意性は、要素のハッシュコードと等値比較演算子によって確認されます。

高速な操作: ハッシュセットは、ハッシュテーブルと呼ばれるデータ構造を使用しており、要素の追加、削除、検索などの操作を平均 O(1) の時間で行います。そのため、大きなコレクションでも効率的に操作することができます。

順序付け: HashSet(Of T) は要素を順序付けません。要素の追加順やその他の基準による順序を保証しません。

可変性: コレクションのサイズは動的に変更されます。要素の追加や削除によって、HashSet(Of T) のサイズが変化します。