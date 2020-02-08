---
title: Subsonic から Google Play Music に移行する
date: 2016-12-05 13:39:32 +0900
---

長い間 Subsonic を使ってきた。一方で Spotify 等のストリーミング配信サービスもたくさん出てきた。
Spotify をちょっと試してみると悪くなくとりあえず有料ユーザになった。

ずっと Spotify を使い続けるか分からないけれど Spotify を契約すると Subsonic のために借りてる VPS の金額が気になってくる。自前でサーバ立てるしかないか... とか考えてたら Google Play Music に音楽をインポートできるよと同僚に教えてもらう。
とはいえどうせそんなにインポートできないだろう、あと Google Play Music の有料ユーザにならないといけないのでは？とか思ったけど

![](/images/google-play-music.png)

全然問題なかった。

Google Play Music 自体が正式に日本でローンチしたのが2015年9月だが無料でインポートできることを全く知らなかった。(あと検索すると日本ローンチ前からもインポートして使ってる人は多そうだった)
とりあえずローカルの PC から音楽を手動でアップロードすると何の問題なくインポートができた。

で VPS からインポートしたい。

https://github.com/simon-weber/gmusicapi という unofficial なライブラリがあって、更にそれを使った https://github.com/thebigmunch/gmusicapi-scripts スクリプトのリポジトリがあった。
Python 3.4 以上が必要で以下作業メモ。

    # m4a を変換するために必要というエラーメッセージが出た
    wget https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-64bit-static.tar.xz
    tar xof ffmpeg-release-64bit-static.tar.xz
    cd ffmpeg-release-64bit-static
    sudo cp ffmpeg-3.2.1-64bit-static/ffmpeg /usr/local/bin

    # Python3 を install してない場合
    sudo apt-get update
    sudo apt-get install python3.4 # gmusicapi-scripts は Python 3.4 以上が必要らしい
    sudo apt install python3-pip

    sudo pip3 install gmusicapi-scripts
    # https://github.com/simon-weber/gmusicapi/issues/60
    gmsync up /path/to/music -U XX:XX:XX:XX:XX:XX
    # => oauth の token を取得する URL が表示されて入力すると upload が開始される

VPS などから upload する時に MAC Address が取得できない時は最初 random にしていたらしい: https://github.com/simon-weber/gmusicapi/issues/60
Google Play Music の Settings のページに以下が書いてあり、

>Listen to your music from any computer as well as up to ten devices. Uploading or downloading music using Music Manager or Google Play Music for Chrome counts towards your device limit. You can deauthorize four devices per year.

random にしているとこの制限にひっかかることから明示的に外から MAC Address を渡せるようにしたようだ。とりあえず自分のローカルの PC の MAC Address を渡すようにした (別のデバイスとしては認識されるが)
あとは nohup で実行して問題なくインポートできてる。時間はそれなりにかかるとは思うが、Subsonic には今までとてもお世話になったので感謝しつつサーバを解約できるので嬉しい。
