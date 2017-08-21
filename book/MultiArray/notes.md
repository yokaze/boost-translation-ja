[Boost.MultiArray](index.md) > その他

# Boost.MultiArray 補足

このページは、[Boost.MultiArray のドキュメント](http://www.boost.org/doc/libs/1_64_0/libs/multi_array/doc/notes.html)の翻訳です。

原文は [HTML のコメントに表記されるとおり](https://github.com/boostorg/multi_array/blob/develop/doc/notes.html) Boost Software Licence, Version 1.0 で提供されています。
この日本語訳の文章は新規に訳出したもので、原文と同等のライセンスで公開します。

## コンパイラのサポート

Boost.MultiArray は各種コンパイラへの対応を念頭に置いて実装しています。
設計上の妥協は最小限となるよう努めていますが、各々のコンパイラは今のところ C++ 標準への対応について問題を抱えているのが普通であり、これらの問題に対する対応が必要です。
このため、Boost.MultiArray は以下のコンパイラでテストを行いました。
1. Gnu C++ コンパイラ v2.95.2（-ftemplate-depth-50 を指定）
2. Gnu C++ コンパイラ v3.0.2
3. Kuck and Associates C++ コンパイラ v4.0f
4. Microsoft Visual C++ v6.0 sp5
5. Comeau C++ コンパイラ v4.2.45 beta2 (libcomo beta 14)
6. Intel C++ コンパイラ v5.0 (+ MS lib v6.0 sp5)
7. Metrowerks CodeWarrior C++ コンパイラ v7.1

既知の問題として、Boost.MultiArray は Borland C++ v5.5.1 で動作しないことが分かっています。
これは、将来のバージョンで対応予定です。

## 今後の予定

以下は、MultiArray の初回バージョンが Boost に追加されたのち、近い将来に実装しようとしている項目のリストです。
1. コンパイラや実装によってパフォーマンスがどのように変わるかを調査し、資料にまとめます。
2. ライブラリの各種クラスを使うためのより広範なサンプルコードを提供します。
3. ユーザードキュメントを読みやすくするために広範囲の書き直しを行います。

---

Ronald Garcia <br />
最終更新: Wed Jan 8 18:09:46 EST 2003

本稿は、[Rue Yokaze](https://github.com/yokaze) が日本語訳をおこないました。
