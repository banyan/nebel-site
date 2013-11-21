---
title: Git の pre-commit hook で未来のコミットをさせないようにする
date: 2013-11-21 10:47:13 +0900
---

最近 Mac で "Set date and time automatically" のチェックを外し、
日付を変えて開発をすることが多いのですが、ありがちなのがそのまま commit してしまうことで、
まだ PR 中に気付ければいいけど、それが merge されてしまって、更にその上に変更をかぶせてしまってたりすると
直すのがなかなか大変でめんどくさい (filter-branch とか使う) ので git の pre-commit でもし日付が変更されている状態なら日付を戻して commit するようにした。
実際は履歴が気持ち悪くなるだけで特に副作用はないけれど。


```
$ sudo lsof -i:123 -P
```

ポートが使われているかどうかは lsof とかで調べればいいけど、チェック自体をコマンドラインからどうやって制御すればいいかなと思ったけど、以下のコマンドがあったことを初めて知った。

```
$ systemsetup -setusingnetworktime on
```

```
$ git config --global init.templatedir '~/.git_template'
```

を実行した後、

https://gist.github.com/banyan/7574810 (Mac のみ対応)

を ~/.git_template/hooks 以下とかに pre-commit という名前で実行権限つけて保存すればOK。
新規のリポジトリを作る際は hooks 以下に pre-commit というファイルができていれば OK で、既存のリポジトリに適用させる場合は git init で reinitialize すればよかった。
