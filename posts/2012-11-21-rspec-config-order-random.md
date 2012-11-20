---
title: randomize rspec example order
date: 2012-11-21 01:05:27 +0900
---

[rspec-rails-2.11.0](http://blog.davidchelimsky.net/2012/07/07/rspec-211-is-released/) (July 7th, 2012) 以降

    $ rails generate rspec:install

rails の generator を使って生成する spec_helper.rb には

    config.order = "random"

という一行が追加されるようになっている。このことで examples のテストの順番がデフォルトでランダムになる。

[Feature idea: allow examples to be run in random order (like minitest)](https://github.com/rspec/rspec-core/issues/403) を見ると、元々は minitest で実装されていたアイディアで、rspec-core 本体では 2.8.0 rc releases の時 (November 6th, 2011) にはすでにリリースされている。(結構前からあるんですね...)

このランダムというのはファイル単位ではなくて example 単位で random になる。そうなることで起こる問題が

1. The documentation and html formatters がランダムにテストが走ると機能しない
1. before(:all) or after(:all) の hook の関係性

を議論している。後者の before(:all) や after(:all) の問題はランダム化する時の実装で含めるようにすれば問題はない、ということになってて、前者のほうは互換性がないときは STDERR にメッセージを送ればいいんじゃないかとか、formatter の whitelist でなんとかなるみたいなことを詳細はちょっとよく分からない。

テストを実行した後に、

    Randomized with seed 37840

という seed が出力されるので、もし random order を再現させたい場合は

    $ bundle exec rspec --order rand:37840

などとして順序を再現することが可能になる。

テストを書いてて、example の間に依存性ができていて、その spec だけを走らせると、テストが通るけど、全体を通すとこける、みたいなことは割とあるし、Travis CI でだけ落ちるとか頭の痛くなる問題なので、
このオプションをつけるようにしてちょっと運用してみよう。
でも適当にライブラリ選んでテスト order = "random" で走らせるとテスト落ちまくるんだけど、なかなか途中からいれると最初大変そうな気もするな...

