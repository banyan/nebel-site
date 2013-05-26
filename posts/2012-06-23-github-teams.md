---
title: GitHub の Organization の権限についてのメモ
date: 2012-06-23 10:37:13 +0900
---

2012-06-23 現在のものなので、
また仕様がすぐに変わるかもしれないですがメモとして書いとく。

(仕様が変わったら更新する予定なのと、間違えてたら教えて頂けるとありがたいです。)

Organization を作ると Team を作ることが可能。
Team には今のところ3つの権限がある。

* Pull Only
* Push & Pull
* Push Pull & Administrative

この他に実質的な管理者である

* Owner

がある。

<table>
  <tr>
    <th>権限</th>
    <th>issue を作るとメールが飛んでくるか</th>
    <th>Pull Request ができるか</th>
    <th>Organization の設定を管理画面からできるか</th>
  </tr>
  <tr>
    <th>Pull Only</th>
    <td>no</td>
    <td>no</td>
    <td>no</td>
  </tr>
  <tr>
    <th>Push & Pull</th>
    <td>yes</td>
    <td>yes</td>
    <td>no</td>
  </tr>
  <tr>
    <th>Push Pull & Administrative</th>
    <td>yes</td>
    <td>yes</td>
    <td>no</td>
  </tr>
  <tr>
    <th>Owner</th>
    <td>yes</td>
    <td>yes</td>
    <td>yes</td>
  </tr>
</table>

---

<blockquote class="twitter-tweet"><p>@<a href="https://twitter.com/banyan">banyan</a> <a href="http://t.co/Cr83XmsBb8" title="http://banyan.github.io/2012/06/23/1/">banyan.github.io/2012/06/23/1/</a> ですが、現在は Push &amp; Pull でも Pull Request できるみたいです。</p>&mdash; m4i (@m4i) <a href="https://twitter.com/m4i/status/325230152978161664">April 19, 2013</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

* 以前は Push & Pull で Pull Request できなかったのですが、@m4i さんに教えて頂いたのでできるように変更しました。(2013/05/26 updated)
* なお、自分のアカウントに対して fork すれば Pull Request は可能。プライベートリポジトリの場合でも fork してもカウントされない。
