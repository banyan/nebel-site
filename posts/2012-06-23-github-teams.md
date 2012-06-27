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
    <td>no</td>
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
