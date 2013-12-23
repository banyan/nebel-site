---
title: jenkins-ci-skip-plugin を書いてみた
date: 2013-12-24 01:40:37 +0900
---

[jenkins-ci-skip-plugin](https://github.com/banyan/jenkins-ci-skip-plugin)

`[ci skip]` という文字列がコミットに含まれているとビルドをスキップするというのが Travis CI にあって、Circle CI も同じ機能がある。

- [Travis CI: How to skip a build](http://about.travis-ci.org/docs/user/how-to-skip-a-build/)
- [Skip a build - CircleCI](https://circleci.com/docs/skip-a-build)

Rails とかはたくさんの environment でテストされているのでドキュメントの変更の場合は必ずコミットに含むように書いてある。

- http://edgeguides.rubyonrails.org/contributing_to_ruby_on_rails.html#contributing-to-the-rails-documentation

で、それを Jenkins でも同じようにできるようにするというもの。あんまり必要は感じてなかったんだけど、最近 Docker とか使ってテストするようになって feature branch とかでテストをパスできるんならしたいなと思うことがあったので書いてみました。

Jenkins は Changeset の差分で見るので、もし Changeset の最新のコミットに文字列が含まれていれば NOT BUILT としてスキップされるけど、そのビルドを手動でもう一度ビルドを走らせる時は Changeset がない状態になるのでビルドが走ります。
