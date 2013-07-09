---
title: mocha-phantomjs@3.1.0 requires mocha.js@1.12.0 or later
date: 2013-07-09 08:49:43 +0100
---

    $ mocha-phantomjs public/test/index.html
    TypeError: 'undefined' is not an object (evaluating 'Mocha.process.stdout')

      mocha-phantomjs/core_extensions.js:17
      mocha-phantomjs/core_extensions.js:58

[mocha-phantomjs@3.1.0](https://github.com/metaskills/mocha-phantomjs) から mocha.js は 1.12.0 以上であることが必要になりました。
後方互換性がない変更のようです。

### refs:

* [use mocha 1.12.x](https://github.com/metaskills/mocha-phantomjs/pull/75)
* [mocha-phantomjs@3.1.0 doesn't work](https://github.com/metaskills/mocha-phantomjs/issues/76)
