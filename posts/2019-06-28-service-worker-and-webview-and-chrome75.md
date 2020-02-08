---
title: service worker + webview + chrome 75 でハマった
date: 2019-06-28T01:23:06+09:00
---

作っているアプリケーションで2019年の5月末から Android での webview で画面が真っ白になってしまうクレームが少しずつ入り始めた。
(一部のページだけを webview で表示している)

真っ白になってしまう、ということは心当たりはあった。

今作っている SPA では S3 でホストしていなくて、Kubernates 上の nginx で静的なファイルをホストしている。(その前に CDN もいる。) そのため、デプロイの度に js やら css やらのファイルは消滅してしまう形になる。CDN の cache があるため、ファイルがなくなっても即座に 404 を返さないが、はじめに話を聞いた時は恐らく特定の android 端末で index.html を cache してしまって存在しないファイルを load しようとして 404 が返ってきてしまって真っ白な画面が表示されていると思った。

が、違った。たまたま社内の android 端末で再現できて、
```
An unknown error occurred when fetching the script
```
というエラーを吐いて service worker が pending の状態で止まっていた。precache しようとしてファイルは全て cancelled という状態になっていた。

webview のバージョンが 75.0.3770.101 でほぼ最新ということから、bugs.chromium.org を探すと完全に同じ内容のバグ報告が見つかった。
[https://bugs.chromium.org/p/chromium/issues/detail?id=977784](https://bugs.chromium.org/p/chromium/issues/detail?id=977784)

同僚の Android エンジニアと相談して対応方法を検討して webview を Chrome Custom tab に切り替える方法を検討したが、諸事情あり一旦はこの webview として読み込まれた場合は service worker を起動させないように変更した。

    export default function register() {
      if (process.env.NODE_ENV === 'production' && 'serviceWorker' in navigator) {
        // The URL constructor is available in all browsers that support SW.
        const publicUrl = new URL(
          process.env.PUBLIC_URL!,
          window.location.toString(),
       );

       if (navigator.userAgent.indexOf('foo') >= 0) {
          navigator.serviceWorker.getRegistrations().then(registrations => {
            for (const registration of registrations) {
              registration.unregister().then(() => {
                window.location.reload();
              });
            }
          });

          return;
        }
    ...

service worker は CRA ベースのスクリプトで workbox を使っている。

この webview として表示する時に UA を渡していたため、判別することが可能で、 マッチした時はまず service worker を register しないという処理にした。

で、すでに service worker をインストール済みだった場合に unregister する、という処理をいれた。 これを service worker がまだインストールされていない状態で開くと、service worker は register されないという期待通りの挙動になった。次に service worker がインストールされた状態で開くと正しく unregister が走り、service worker は register されないという期待通りの挙動になった。

で、すでに [https://bugs.chromium.org/p/chromium/issues/detail?id=977784](https://bugs.chromium.org/p/chromium/issues/detail?id=977784) の状態になっている人はどうなるか、というと unregister が走る前に
```
An unknown error occurred when fetching the script
```
が出て死んでしまう状態になった。結果アプリのデータを消すか uninstall するかでしか戻らない状態になった。

service worker でそんなに問題が起こったことがなかったのだけど改めて起きると大変だなと学んだ。
