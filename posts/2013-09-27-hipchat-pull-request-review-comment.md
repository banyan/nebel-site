---
title: HipChat の pull-request-review-comment を既存の hook に適用させる
date: 2013-09-27 10:01:37 +0900
---

HipChat の GitHub Integration はデフォルトの event が多くて便利だけど、
pull-request-review-comment はサポートとされてなかったけどやっと最近デフォルトで入った。

* [Send pull_request_review_comment events to HipChat](https://github.com/github/github-services/pull/695)

自分もその前の [pr](https://github.com/github/github-services/pull/648) に微力ながら +1 とかしてたので非常に嬉しいけど
technoweenie さんが[ここ](https://github.com/github/github-services/pull/695#issuecomment-24836222) で言ってるように
既存の hook には適用されない。update でもダメで、octokit で delete して create してもうまく適用されなかった。
edit で今の default_event を全部適用させるようにしてみて届くようにしました。

* [update-hipchat-hooks-event.rb](https://gist.github.com/banyan/6718410)

(というか元から API 経由だったら適用できたっぽいことを知らなかった
