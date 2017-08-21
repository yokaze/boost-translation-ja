[Boost.MultiArray](index.md) > テストケースの概要

# Boost.MultiArray テストの概要

このページは、[Boost.MultiArray のドキュメント](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/doc/test_cases.html)の翻訳です。

原文は [HTML のコメントに表記されるとおり](https://github.com/boostorg/multi_array/blob/develop/doc/test_cases.html) Boost Software Licence, Version 1.0 で提供されています。
この日本語訳の文章は新規に訳出したもので、原文と同等のライセンスで公開します。

このページでは、Boost.MultiArray (B.M) に含まれるテストケースの概要を説明します。

## 用語

ここでは本文の説明のため、以下の用語を使います。

- **B.M** - Boost.MultiArray の略。
- **主要クラス** - ```boost::multi_array```, ```boost::const_multi_array_ref``` および ```boost::multi_array_ref```。
- **配列型** - すべての主要クラス、および以下に列挙する主要クラスの入れ子型。主要クラスを ```array``` とすると、```array::const_subarray```, ```array::subarray```, ```array::const_array_view``` および ```array::array_view```。

## 整合性テスト

以下のテストでは、Boost.MultiArray の様々な機能が使用時に正しく動作することを検証します。

<style type="text/css">
<!--
.markdown-section table {
    table-layout: fixed;
    display: block;
    overflow-x: auto;
}
-->
</style>

| プログラム | 説明 |
|:-|:-|
| [libs/multi_array/test/constructors.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/constructors.cpp) | B.M の主要クラスの全コンストラクタをテストします。 |
| [libs/multi_array/test/access.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/access.cpp) | B.M のすべての配列型にたいして ```[]``` および ```()``` 演算子をテストします。 |
| [libs/multi_array/test/compare.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/compare.cpp) | B.M の主要クラスの全比較演算子をテストします。 |
| [libs/multi_array/test/iterators.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/iterators.cpp) | B.M のすべての配列型について、イテレータの移動とアクセス処理をテストします。 |
| [libs/multi_array/test/slice.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/slice.cpp) | B.M のすべての配列型について、全パターンの部分ビューの作成をテストします。 |
| [libs/multi_array/test/assign.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/assign.cpp) | B.M のさまざまな配列型について、代入演算子 ```()``` の動作をテストします。 |
| [libs/multi_array/test/assign_to_array.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/assign_to_array.cpp) | ```multi_array``` が他の任意の配列型から作成できることを確認します。 |
| [libs/multi_array/test/index_bases.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/index_bases.cpp) | B.M の主要クラスについて、インデックスの再割り振りができることを確認します。 |
| [libs/multi_array/test/storage_order_convert.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/storage_order_convert.cpp) | メモリ配置型間の変換をテストします。 |
| [libs/multi_array/test/storage_order.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/storage_order.cpp) | B.M の主要クラスについて、さまざまな storage_order をテストします。 |
| [libs/multi_array/test/reshape.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/reshape.cpp) | B.M の主要クラスのリシェイプ機能をテストします。 |
| [libs/multi_array/test/range1.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/range1.cpp) | ```array::index_range``` を使ってインデックス範囲を指定するさまざま構文をテストします。 |
| [libs/multi_array/test/idxgen1.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/idxgen1.cpp) | ```array::index_gen``` 型のインスタンスをテストします。 |
| [libs/multi_array/test/stl_interaction.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/stl_interaction.cpp) | 配列型と STL コンテナの相互運用をテストします。 |
| [libs/multi_array/test/resize.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/resize.cpp) | ```multi_array``` クラスのリサイズ処理をテストします。 |
| [libs/multi_array/test/concept_checks.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/concept_checks.cpp) | すべての配列型が定義済みのコンセプトに合致しているかをテストします。 |
| [libs/multi_array/test/assert.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/assert.cpp) | ユーザーが本ライブラリの BOOST_ASSERT をカスタマイズできることをテストします。 |
| [libs/multi_array/test/generative_tests.hpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/generative_tests.hpp) | 全ての配列型にたいしてテストを簡単に実行するためのハーネスプログラムです。slice.cpp, iterators.cpp および access.cpp で使っています。 |

## コンパイルを失敗させるテスト

以下のテストでは、不正なオブジェクト生成プログラムがコンパイルに失敗することを検証します。
コンパイルの失敗を検証しやすくするため、各テストケースはエラーが発生する構文をひとつだけテストします。

| プログラム | 説明 |
|:-|:-|
| [libs/multi_array/test/fail_cbracket.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_cbracket.cpp)<br />[libs/multi_array/test/fail_ref_cbracket.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_cbracket.cpp) | const 配列型の ```operator[]``` は要素を変更できない。 |
| [libs/multi_array/test/fail_cdata.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_cdata.cpp)<br />[libs/multi_array/test/fail_ref_cdata.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_cdata.cpp) | ```array::data() const``` はデータ領域の const なポインタを返さなければならない。 |
| [libs/multi_array/test/fail_citerator.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_citerator.cpp)<br />[libs/multi_array/test/fail_ref_citerator.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_citerator.cpp) | ```const_iterator``` を ```iterator``` に変換することはできない。 |
| [libs/multi_array/test/fail_cparen.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_cparen.cpp)<br />[libs/multi_array/test/fail_ref_cparen.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_cparen.cpp) | const 配列型の ```operator()``` は要素を変更してはならない。 |
| [libs/multi_array/test/fail_criterator.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_criterator.cpp)<br />[libs/multi_array/test/fail_ref_criterator.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_criterator.cpp) | ```const_reverse_iterator``` を ```reverse_iterator``` に変換することはできない。 |
| [libs/multi_array/test/fail_csubarray.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_csubarray.cpp)<br />[libs/multi_array/test/fail_ref_csubarray.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_csubarray.cpp) | ```const_subarray``` を ```subarray``` に変換することはできない。 |
| [libs/multi_array/test/fail_csubarray2.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_csubarray2.cpp)<br />[libs/multi_array/test/fail_ref_csubarray2.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_csubarray2.cpp) | ```array::operator[] const``` を ```subarray``` に変換することはできない。 |
| [libs/multi_array/test/fail_csubarray3.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_csubarray3.cpp)<br />[libs/multi_array/test/fail_ref_csubarray3.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_csubarray3.cpp) | 配列の ```const_subarray``` は要素を変更してはならない。 |
| [libs/multi_array/test/fail_cview.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_cview.cpp)<br />[libs/multi_array/test/fail_ref_cview.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_cview.cpp) | 配列の ```const_array_view``` は要素を変更してはならない。 |
| [libs/multi_array/test/fail_cview2.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_cview2.cpp)<br />[libs/multi_array/test/fail_ref_cview2.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_cview2.cpp) | ```array::operator[] const``` を ```array_view``` に変換することはできない。 |
| [libs/multi_array/test/fail_cview3.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_cview3.cpp)<br />[libs/multi_array/test/fail_ref_cview3.cpp](http://www.boost.org/doc/libs/1_65_0/libs/multi_array/test/fail_ref_cview3.cpp) | 配列の ```const_array_view``` は要素を変更してはならない。 |

<table>
    <tr valign=top style="background-color: transparent; border-style: none;">
        <td nowrap style="border-style: none;">Copyright &copy 2001</td>
        <td style="border-style: none;">
<a href="http://www.boost.org/people/ronald_garcia.htm">Ronald Garcia</a>, Indiana University
(garcia [at] cs.indiana.edu)<br />
<a href="http://www.boost.org/people/jeremy_siek.htm">Jeremy Siek</a>, Indiana University
(jsiek [at] cs.indiana.edu)<br />
<a href="http://www.lsc.nd.edu/~lums">Andrew Lumsdaine</a>, Indiana University
(lums [at] cs.indiana.edu)
        </td>
    </tr>
</table>

---

Ronald Garcia <br />
最終更新: Wed Oct 31 19:46:44 EST 2001

本稿は、[Rue Yokaze](https://github.com/yokaze) が日本語訳をおこないました。
