---
title: Can't focus on iframe with IE
date: 2015-01-17 17:24:06 -0500
---

iframe with IE (IE11) でハマった。

    var ChatComponent = React.createClass({
      render: function() {
        return (
          <div>
            <input type="text" />
          </div>
        );
      }
    });

という component を iframe 上で表示していて、最初の iframe を生成する時は問題ないが、一度 component を削除して2回目に iframe を生成すると文字が入力できない状態になってしまう (focus されない)

    var ChatComponent = React.createClass({
      render: function() {
        return (
          <div>
            <input type="text" autoFocus />
          </div>
        );
      }
    });

autofocus attribute をつけるとうまくいく。2015年でも IE を疑う気持ちを忘れてはいけないことを学んだ。

refs: http://bugs.jquery.com/ticket/12319
