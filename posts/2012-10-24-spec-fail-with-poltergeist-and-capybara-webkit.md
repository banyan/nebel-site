---
title: capybara + poltergeist か capybara + capybara-webkit でテストが通らない問題にぶつかった
date: 2012-10-24 02:16:41 +0900
---

### はじめに

この情報は 2012-10-24 のものです。これ以降ではこの回避方法より正式に upgrade するだけで問題が解決する可能性がありますのでご確認ください。
現状日本語の情報がなかったので書きました。また正式な対応があり次第 update する予定です。何かありましたら [@banyan](http://twitter.com/banyan) まで教えて頂けるとありがたいです。

### 現象

#### [poltergeist](https://github.com/jonleighton/poltergeist) を使っている場合

* js => true にしていると1つ目のテストは通るが js => true にしている2つ目のテストは落ちる
* 正確にいうと奇数のテストは通るが、偶数のテストは落ちる
* エラー内容は以下のような感じ

```
Failure/Error: visit "/"
    Capybara::Poltergeist::DeadClient:
    The PhantomJS client died while processing {"name":"visit","args":["http://127.0.0.1:63091/"]}
{"name"=>"visit", "args"=>["http://127.0.0.1:63091/"]}
PhantomJS has crashed. Please read the crash reporting guide at https://code.google.com/p/phantomjs/wiki/CrashReporting and file a bug report at https://code.google.com/p
```

* capybara (1.1.2)
* poltergeist (1.0.1)

#### [capybara-webkit](https://github.com/thoughtbot/capybara-webkit) を使っている場合

* はじめに Capybara::Driver::Webkit::WebkitNoResponseError がでて以降は Errno::EPIPE: Broken pipe が出て全て死ぬ

```Capybara::Driver::Webkit::WebkitNoResponseError:
No response received from the server.
```

* capybara (1.1.2)
* capybara-webkit (0.12.1)

#### issue とか調べていくと、font-awesome を使っている人が起こっている...（ ﾟдﾟ）

* https://github.com/thoughtbot/capybara-webkit/issues/68
* https://github.com/thoughtbot/capybara-webkit/issues/181
* https://github.com/jonleighton/poltergeist/issues/44
* Mac の font が原因? まだ正式に答えは出てなさそう。

#### テストの時は font-awesome を使わないようにする方法でとりあえず回避した

* https://github.com/thoughtbot/capybara-webkit/issues/181#issuecomment-6809202

#### 所感

* そもそも JS の example が少なかったし導入のタイミングだったので切り分けで時間がかかった。
 * 元々通ってたのが gem の update をして気づいたのならすぐ気づけたと思った。
* poltergeist(phantomjs) 諦めて capybara-webkit でも同じように死んだので気づけました。
