[Boost.MultiArray](index.md) > 使い方とチュートリアル

# Boost 多次元配列ライブラリ (Boost.MultiArray)

このページは、[Boost.MultiArray のドキュメント](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/doc/user.html)の翻訳です。

原文は [HTML のコメントに表記されるとおり](https://github.com/boostorg/multi_array/blob/develop/doc/user.html) Boost Software Licence, Version 1.0 で提供されています。
この日本語訳の文章は新規に訳出したもので、原文と同等のライセンスで公開します。

## 概要
Boost 多次元配列ライブラリは、連続したデータ領域に対する一次元配列インターフェースと同等の構文をもつクラステンプレートを多次元配列に対して提供します。
本ライブラリの各種クラスはコンセプトベースのジェネリックプログラミングで実装されており、統一されたインターフェースを提供します。
このインターフェース設計は、C++ の STL コンテナに沿ったものです。
Boost.MultiArray は既存の方法（特に、```std::vector<std::vector<...>>``` を使って N 次元配列を表現する方法）にくらべて、柔軟かつ効率的に N 次元配列を表現します。
本ライブラリで提供する配列型は、C++ の組み込み配列型とよく似た方法で使うことができます。
また、リサイズ、リシェイプ（配列全体の要素数を保ったまま各次元のサイズを組み替えること）、ビューの作成などをサポートします。
これらの機能について、以下の文章で説明します。

## 目次
1. [はじめに](#introduction)
2. [簡単な例](#short-example)
3. [MultiArray のクラス](#multiarray-components)
4. [コンストラクタと代入](#construction-and-assignment)
5. [配列ビューと部分配列のジェネレータ](#array-view-and-subarray-type-generators)
6. [配列の次元数の指定](#specifying-array-dimensions)
7. [配列要素へのアクセス](#accessing-elements)
8. [ビューの作成](#creating-views)
9. [メモリ配置](#storage-ordering)
10. [配列の原点の設定](#setting-the-array-base)
11. [配列の形の変更](#changing-an-arrays-shape)
12. [配列のリサイズ](#resizing-an-array)
13. [MultiArray のコンセプト](#multiarray-concept)
14. [テストケース](#test-cases)
15. [関連する内容](#related-work)
16. [著者リスト](#credits)

## はじめに<a id="introduction"></a>
C++ の標準ライブラリには汎用的なコンテナの実装がいくつか含まれていますが、多次元配列はサポートされていません。
```std::vector``` クラステンプレートを使えば ```std::vector<std::vector<double>>``` のような形で ```double``` の2次元配列を表現したり、より一般の N 次元配列を表現することはできます。
しかし、多次元配列の API としては扱いにくく、メモリアクセスのオーバーヘッドも非常に大きくなる場合があります。
C++ の組み込み配列（```int arr[2][2][2];``` など）は C++ の標準ライブラリと直接相互に連携することができず、関数の呼び出し境界を超えると（最後の次元のサイズのような）情報を失ってしまいます。
最後に、動的に確保された連続メモリ領域は配列として扱うことができますが、この方法では配列のサイズを手動で管理する必要があります。
この方法はエラーを引き起こしやすく、効率的なソフトウェア開発を阻害してしまいます。

Boost.MultiArray ライブラリは C++ の標準コンテナを拡張するもので、さまざまな多次元配列の抽象化をおこないます。
このライブラリは汎用的な配列型のクラステンプレートと、組み込み配列を拡張するアダプター型を含んでおり、このアダプター型を使うと慣用的な配列操作や、C++ の STL ライブラリのコンテナ型、アルゴリズムとの相互運用をサポートすることができます。
各種の配列型は共通のインターフェースを実装しており、これは一般的な配列のアルゴリズムを実装することのできるジェネリックプログラミングの方法に基づいています。

この資料では、MultiArray のクラス群について簡単なチュートリアルとユーザーガイドを提供し、基本的で共通の利用パターンについて説明します。
ライブラリの機能について完全かつ厳密なドキュメントを必要とする場合は、リファレンスページを参照してください。

## 簡単な例<a id="short-example"></a>
次に示すのは、```multi_array``` の基本的な使用例です。
```cpp
#include "boost/multi_array.hpp"
#include <cassert>

int
main () {
  // 3 x 4 x 2 の 3 次元配列を作成
  typedef boost::multi_array<double, 3> array_type;
  typedef array_type::index index;
  array_type A(boost::extents[3][4][2]);

  // 配列の各要素に値を代入
  int values = 0;
  for(index i = 0; i != 3; ++i)
    for(index j = 0; j != 4; ++j)
      for(index k = 0; k != 2; ++k)
        A[i][j][k] = values++;

  // 値の検証
  int verify = 0;
  for(index i = 0; i != 3; ++i)
    for(index j = 0; j != 4; ++j)
      for(index k = 0; k != 2; ++k)
        assert(A[i][j][k] == verify++);

  return 0;
}
```

## MultiArray のクラス<a id="multiarray-components"></a>

Boost.MultiArray (boost/multi_array.hpp) はユーザー向けのテンプレート型を三種類提供します。

1. ```multi_array```
2. ```multi_array_ref```
3. ```const_multi_array_ref```

```multi_array``` はコンテナ型のテンプレートです。
```multi_array``` のインスタンスは、作成時に指定された次元数と要素数にもとづきメモリを確保します。
この型はデフォルトコンストラクタで初期化することができ、その後必要に応じてリサイズすることができます。

```multi_array_ref``` は既存の配列上に ```multi_array``` のインターフェースを作成します。
この型のインスタンスはメモリ上のデータを所有しません。

```const_multi_array_ref``` は ```multi_array_ref``` と類似した型で、データの不変性を保証します。
この型は、```T const*``` 型のポインタをラップすることができます。

これらの三種類の型はとても似たふるまいをします。
コンストラクタの構成を除き、```multi_array``` と ```multi_array_ref``` は同等のインターフェースを提供します。```const_multi_array_ref``` は ```multi_array_ref``` の実装のうち、const 性を保証する部分だけを実装します。

## コンストラクタと代入<a id="construction-and-assignment"></a>
配列の型（```multi_array```, ```multi_array_ref``` および ```const_multi_array_ref```）には目的に合わせた複数のコンストラクタがあります。
詳細はリファレンスページを参照してください。

非 const なすべての配列型には代入演算子 ```operator=()``` が定義されています。
```multi_array```, ```multi_array_ref```, ```subarray``` および ```array_view``` には形の一致する他のインスタンスから代入をすることができます。
const な型、```const_multi_array_ref```, ```const_subarray``` および ```const_array_view``` は同じ形の配列への代入元として使うことができます。
代入は配列のディープ（要素対要素）コピーとなります。

## 配列ビューと部分配列のジェネレータ<a id="array-view-and-subarray-type-generators"></a>

いくつかのケースでは、配列ビューや部分配列型のためのネストされたジェネレータが問題となることがあります。
たとえば、配列型でパラメータ付けされた関数テンプレートの内部では ```template``` キーワードが余分に必要となり、可読性を下げてしまいます。
さらによくあるのは、プログラムのコンパイラがテンプレートパラメータにネストされたテンプレートを処理できないケースです。
このため、```subarray_gen```, ```const_subarray_gen```, ```array_view_gen```, ```const_array_view_gen``` というように複数のジェネレータが存在します。
したがって、以下の二通りの ```typedef``` は同じ型を表現することになります。

```cpp
template <typename Array>
void my_function() {
  typedef typename Array::template array_view<3>::type view1_t;
  typedef typename boost::array_view_gen<Array,3>::type view2_t;
  // ...
}
```

## 配列の次元数の指定<a id="specifying-array-dimensions"></a>

Boost.MultiArray の配列型のインスタンスを作成するとき、ほとんどの場合には次元数と各次元の範囲を指定する必要があります（```boost::multi_array``` にはデフォルトコンストラクタもあります）。
配列の次元数は必ずテンプレートパラメータとして指定する必要がありますが、各次元の範囲は二通りの異なった方法で指定することができます。

最初の方法では、 ```boost::array``` のような型を使いコンストラクタに各次元の範囲の [Collection](http://www.boost.org/doc/libs/1_65_0/libs/utility/Collection.html) オブジェクトをあたえます。
コンストラクタはコンテナの起点となるイテレータを受け取り、N 個の要素を取得します。
これは N 個の次元の範囲に対応しています。
この仕組みは、次元数に依存しないコードを書く場合に有用です。

### 実装例

```cpp
typedef boost::multi_array<double, 3> array_type;
boost::array<array_type::index, 3> shape = {{ 3, 4, 2 }};
array_type A(shape);
```

二つ目の方法では、行列の次元数に対応した ```extent_gen``` 型のオブジェクトをコンストラクタにあたえます。
```extent_gen``` 型は ```multi_array_types``` 名前空間およびすべての配列型の入れ子型として定義されており、デフォルトでは ```extent_gen``` 型のグローバルオブジェクトが ```boost::extents``` という名前で作成されます。
このオブジェクトのためのメモリ消費が問題になる場合は、ライブラリのヘッダファイルをインクルードする前に ```BOOST_MULTI_ARRAY_NO_GENERATORS``` を定義することでオブジェクトの作成を拒否することができます。

### 実装例

```cpp
typedef boost::multi_array<double, 3> array_type;
array_type A(boost::extents[3][4][2]);
```

## 配列要素へのアクセス<a id="accessing-elements"></a>

Boost.MultiArray では二通りの方法で配列要素にアクセスすることができます。
一つ目の方法では、通常のC言語の配列と同じように ```[]``` 演算子で配列要素を参照します。

### 実装例

```cpp
typedef boost::multi_array<double, 3> array_type;
array_type A(boost::extents[3][4][2]);
A[0][0][0] = 3.14;
assert(A[0][0][0] == 3.14);
```

二つ目の方法では、```()``` 演算子に添え字の [Collection](http://www.boost.org/doc/libs/1_65_0/libs/utility/Collection.html) をあたえます。
MultiArray の各配列型はこの [Collection](http://www.boost.org/doc/libs/1_65_0/libs/utility/Collection.html) オブジェクトから次元数に対応した N 個の添え字を取得し、配列の要素にアクセスします。

### 実装例

```cpp
typedef boost::multi_array<double, 3> array_type;
array_type A(boost::extents[3][4][2]);
boost::array<array_type::index,3> idx = {{0,0,0}};
A(idx) = 3.14;
assert(A(idx) == 3.14);
```

この方法では配列の次元数に依存しないプログラムを書くことができ、いくつかのコンパイラでは ```[]``` 演算子より高速なマシンコードを生成します。

明示的な指定がない場合、配列にアクセスするための ```()``` および ```[]``` 演算子は範囲の確認をおこないます。
指定されたインデックスが配列の境界を超える場合、アサーションが発行されプログラムは終了します。
（製品のリリース時など、性能上の観点から）境界チェックを無効にする場合は、アプリケーションが multi_array.hpp をインクルードする前に ```BOOST_DISABLE_ASSERTS``` マクロを定義してください。

## ビューの作成<a id="creating-views"></a>

Boost.MultiArray では、既存の配列から部分ビューを作成することができます。
部分ビューの次元数は元の配列と同じか、または少なくすることができます。

部分ビューは ```[]``` 演算子に ```index_gen``` 型のオブジェクトを指定して作ることができます。
また、```index_gen``` 型のインスタンスは ```[]``` 演算子に ```index_range``` 型のオブジェクトをあたえることで作成できます。
```index_range``` および ```index_gen``` 型は ```multi_array_types``` 名前空間およびすべての配列型の入れ子型として定義されています。
```boost::extents``` と同じように、デフォルトでは ```boost::indices``` という名前のグローバルオブジェクトが作成されます。
ライブラリのヘッダをインクルードする前に ```BOOST_MULTI_ARRAY_NO_GENERATORS``` を定義することでこのオブジェクトの作成を拒否することができます。
以下に、部分ビューを作成する簡単な具体例を掲載します。

### 実装例

```cpp
// myarray = 2 x 3 x 4

//
// array_view の各次元: [原点,上限) (各次元のデフォルトのストライド = 1)
// dim 0: [0,2)
// dim 1: [1,3)
// dim 2: [0,4) （ストライド = 2）,
//

typedef boost::multi_array_types::index_range range;
// または typedef array_type::index_range range;
array_type::array_view<3>::type myview =
  myarray[ boost::indices[range(0,2)][range(1,3)][range(0,4,2)] ];

for (array_type::index i = 0; i != 2; ++i)
  for (array_type::index j = 0; j != 2; ++j)
    for (array_type::index k = 0; k != 2; ++k)
      assert(myview[i][j][k] == myarray[i][j+1][k*2]);
```

```index_gen``` に定数をあたえることで、元の配列より少ない次元数の部分ビューを作成することができます（この処理はスライス化と呼ばれています）。

### 実装例

```cpp
// myarray = 2 x 3 x 4

//
// array_view の各次元:
// [原点,ストライド,上限)
// [0,1,2), 1, [0,2,4)
//

typedef boost::multi_array_types::index_range range;
array_type::index_gen indices;
array_type::array_view<2>::type myview =
  myarray[ indices[range(0,2)][1][range(0,4,2)] ];

for (array_type::index i = 0; i != 2; ++i)
  for (array_type::index j = 0; j != 2; ++j)
    assert(myview[i][j] == myarray[i][1][j*2]);
```

### index_range についての補足

```index_range``` 型を使って部分ビューを作る際、複数の方法で範囲を指定することができます。
以下の例はすべて同じ範囲を指定しています。

### 実装例

```cpp
// [原点,ストライド,上限)
// [0,2,4)

typedef boost::multi_array_types::index_range range;
range a_range;
a_range = range(0,4,2);
a_range = range().start(0).finish(4).stride(2);
a_range = range().start(0).stride(2).finish(4);
a_range = 0 <= range().stride(2) < 4;
a_range = 0 <= range().stride(2) <= 3;
```

スライス演算の際、```index_range``` 型のオブジェクトに対する始点・終点の設定を省略すると、このオブジェクトは元の配列の範囲を継承します。
この機能を使うと、ある種のコードを配列の範囲に関する知識なしに実装することができます。
より具体的に言うと、デフォルトコンストラクタで作成された range オブジェクトは対応する次元のすべての要素を部分ビューに組み入れます。

### 実装例

```cpp
typedef boost::multi_array_types::index_range range;
range a_range;

// この次元のすべての要素
a_range = range();

// 3 <= i となるような添字 i
a_range = range().start(3)
a_range = 3 <= range();
a_range = 2 < range();

// i < 7 となるような添字 i
a_range = range().finish(7)
a_range = range() < 7;
a_range = range() <= 6;
```

次のプログラムは、これらの記法の具体例です。

```cpp
// 第 1 次元からすべての範囲を取得
// 第 2 次元から i < 5 の範囲を取得
// 第 3 次元から 4 <= j <= 7 の範囲をストライド 2 で取得
myarray[ boost::indices[range()][range() < 5][4 <= range().stride(2) <= 7] ];
```

## メモリ配置<a id="storage-ordering"></a>
各配列型のコンストラクタにはメモリ配置を引数として指定することができます。
この機能は FORTRAN のように、標準の C 言語とことなるメモリ配置を要求する既存コードと連携する場合に力を発揮します。
選択可能なメモリ配置は ```c_storage_order```, ```fortran_storage_order```, ```general_storage_order``` です。

```c_storage_order``` はデフォルトのメモリ配置で、C 言語の配列と同じ方法で値をメモリに格納します。
この方法では、データは最後の次元から順番に並べられます。

```fortran_storage_order``` を使うと、FORTRAN と同じ方法で配列をメモリに格納することができます。
この方法では、データは最初の次元から順番に並べられます。
storage_order は配列の原点の設定とは関係なく、```fortran_storage_order``` を指定しても配列は0オリジンであることに注意してください。

### 実装例
```cpp
typedef boost::multi_array<double,3> array_type;
array_type A(boost::extents[3][4][2],boost::fortran_storage_order());
call_fortran_function(A.data());
```

```general_storage_order``` を使うと、配列がメモリに格納される順番と、各軸が昇順か降順かを個別に指定することができます。

### 実装例

```cpp
typedef boost::general_storage_order<3> storage;
typedef boost::multi_array<int,3> array_type;

// 最後の次元、最初の次元、中央の次元の順番で格納する
array_type::size_type ordering[] = {2,0,1};

// 最初の軸（第0次元）を降順で格納する
bool ascending[] = {false,true,true};

array_type A(extents[3][4][2],storage(ordering,ascending));
```

## 配列の原点の設定<a id="setting-the-array-base"></a>
使用目的によっては、0オリジンの配列を使うことが適切でなかったり、美しくない場合があります。
Boost.MultiArray では、配列の原点を変更するために二通りの方法を使うことができます。
一つ目は ```extent_range``` 型を通して ```extent_gen``` 型のコンストラクタに範囲の両端の値を伝える方法です。

### 実装例
```cpp
typedef boost::multi_array<double, 3> array_type;
typedef boost::multi_array_types::extent_range range;
// または typedef array_type::extent_range range;

array_type::extent_gen extents;

// 0番目の次元: 0オリジン
// 1番目の次元: 1オリジン
// 2番目の次元: -1オリジン
array_type A(extents[2][range(1,4)][range(-1,3)]);
```

別の方法では、標準的な手順で配列を作成したのち原点を変更することができます。
すべての次元の原点を同じ値にする場合は、```reindex``` メンバ関数に原点の値をひとつだけ指定します。

### 実装例
```cpp
typedef boost::multi_array<double, 3> array_type;

array_type::extent_gen extents;

array_type A(extents[2][3][4]);
// 1オリジンに変更
A.reindex(1)
```

また、次の例では、各次元の原点を ```reindex``` メンバ関数でそれぞれ指定しています。
関数には各次元の原点に対応する [Collection](http://www.boost.org/doc/libs/1_65_0/libs/utility/Collection.html) を渡します。

### 実装例
```cpp
typedef boost::multi_array<double, 3> array_type;

array_type::extent_gen extents;

// 0番目の次元: 0オリジン
// 1番目の次元: 1オリジン
// 2番目の次元: -1オリジン
array_type A(extents[2][3][4]);
boost::array<array_type::index,ndims> bases = {{0, 1, -1}};       
A.reindex(bases);
```

## 配列の形の変更<a id="changing-an-arrays-shape"></a>
Boost.MultiArray の配列型はリシェイプ処理をサポートします。
すなわち、配列の次元数と全体の要素数が一致するようにして新しい形状を設定することができます。

### 実装例
```
typedef boost::multi_array<double, 3> array_type;

array_type::extent_gen extents;
array_type A(extents[2][3][4]);
boost::array<array_type::index,ndims> dims = {{4, 3, 2}};       
A.reshape(dims);
```

配列のリシェイプ処理は各次元の原点の設定とは無関係であることに注意してください。

## 配列のリサイズ<a id="resizing-an-array"></a>
```boost::multi_array``` は内容を保ったままサイズを変更することができます。
リサイズの前後では次元数を揃える必要がありますが、各次元の範囲は任意に拡大・縮小することができます。
配列のサイズがすべての次元で同じか、拡大される場合、既存の要素は新しく確保されたメモリ領域へコピーされ、続いて古いメモリ領域の要素が破棄されます。
配列に新しく確保された要素はデフォルトのコンストラクタで初期化されます。
また、いくつかの次元で配列のサイズが縮小される場合、この範囲の要素は破棄されます。

### 実装例

```cpp
typedef boost::multi_array<int, 3> array_type;

array_type::extent_gen extents;
array_type A(extents[3][3][3]);
A[0][0][0] = 4;
A[2][2][2] = 5;
A.resize(extents[2][3][4]);
assert(A[0][0][0] == 4);
// A[2][2][2] は有効な式ではなくなっています。
```

## MultiArray のコンセプト<a id="multiarray-concept"></a>
Boost.MultiArray は実装のために [MultiArray コンセプト](reference.md#multiarray-concept) を定義しています。
このコンセプトでは N 次元配列のインターフェースを定義しています。

## テストケース<a id="test-cases"></a>
Boost.MultiArray には、ライブラリの機能と構文を検証するためのテストスイートが付随しています。
テストケースの内容は[リンク先](test_cases.md)に記載しています。

## 関連する内容<a id="related-work"></a>
```boost::array``` と ```std::vector``` はユーザーのデータを格納するための一次元のコンテナで、メモリの管理をおこないます。
```std::valarray``` は C++ 標準ライブラリに含まれる低レベルの実装で、数値演算アプリケーションのための高速かつ可搬性の高い実装を提供します。
[Blitz++](http://blitz.sourceforge.net/) は Todd Veldhuizen によって開発された配列のライブラリで、高度な C++ のテクニックにより配列ベースの数値演算アプリケーションで Fortran に近い性能を出すことができます。**array_traits** は以前 Boost で配布されていたベータ版のライブラリで、C++ の標準配列にイテレータを実装します。
本ライブラリは、```boost::array``` が C スタイルの一次元配列を拡張するのと同じような方法で、C スタイルの N 次元配列を拡張するものです。

## 著者リスト<a id="credits"></a>
- Ronald Garcia: ライブラリの主な開発を担当しました。
- Jeremy Siek: ライブラリの実装を手伝い、素晴らしいアイディアと助言をくださいました。また、Microsoft Visual C++ への対応を手伝って下さいました。
- Giovanni Bavestrelli: N次元配列のプロトタイプ実装を開発して下さいました。この実装は、Boost メーリングリストのメンバーのフィードバックから着想を得ています。本ライブラリの設計はいくつかの面で、この実装とコメントを元にしています。
- Todd Veldhuizen: 彼は Blitz++ の開発者で、本ライブラリのいくつかの機能を設計する上で影響をうけています。また、彼はライブラリの設計や実装についてフィードバックを下さいました。
- Jeremiah Willcock: 実装やライブラリの設計についてフィードバックを下さり、また機能についていくつかの助言を下さいました。
- Beman Dawes: ライブラリを Microsoft Windows コンパイラで扱えるようにするため、多大な助言を下さいました。

---

Ronald Garcia <br />
最終更新: Tue Feb 7 17:15:50 EST 2006

本稿は、[Rue Yokaze](https://github.com/yokaze) が日本語訳をおこないました。
