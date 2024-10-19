エビングハウスの忘却曲線について
https://smbiz.asahi.com/article/14828411

- 毎日21時ぐらいに LINE か Slack に送られるようにする
- 当日登録した内容は当日送られる

7/27(Satur)
使用技術について

Hono と Cloudflare Workers を使ってやってみる。
API は Hono で作って登録、表示、編集などをする。
Workers の Cron Triggers で忘却曲線に沿って LINE API を使って LINE に通知する。
コンテンツは CloudFlare D1 に保存する。

フロントは簡単でいいので UI コンポーネントがある Material UI か Chakra UI がいいか。

9/2(Mon)
まずは D1 に暗記データを置き、 Workers の Cron Triggers で LINE Bot に通知を送るようにする。
D1 を触ったことがないので調べる。

D1 についての記事
https://zenn.dev/mizchi/articles/cloudflare-d1

サーバーレスで以下のような構成ができるらしい
https://www.accelia.net/column/cloudflaretips/7115/
データベース：Cloudflare D1
バックエンド(API)：Cloudflare Workers
フロントエンド：Cloudflare Pages(react)

LINE Botの開発でCloudflareとHonoを使う理由
https://zenn.dev/sh1n4ps/articles/062d5b51bf75ad

9/10(Tue)
開発の進め方は以下が参考になる。
https://qiita.com/Unemployed_jp/items/32b8612fd47ca4ec76b3

[CloudFlare Workers、Cloudflare D1、HonoでLINE botを作りました](https://tkancf.com/blog/creating-line-bot-with-cloudflare-workers-d1-and-hono)

9/11(Wed)
まずはちゃっちゃとプロトタイプを作る。以下の2つに分けられると思う。

1. Web ページで入力したコンテンツを D1 に送信して保存する
2. D1 に保存されたコンテンツを定期的に LINE Bot で送信する

https://zenn.dev/prog_daiki/books/8f186445cc080e/viewer/7c2df8


9/18(Wed)
とりあえずサクッと作ってみる。
WSL に Forgetting-curve というディレクトリ名で Next のプロジェクトを作った。
その中に my-app ディレクトリに Hono のプロジェクトも作った。

以下のコマンドで D1 のデータベースも作った。
npx wrangler d1 create forgetting-curve

https://qiita.com/kmkkiii/items/2b22fa53a90bf98158c0

9/24(Tues)
https://qiita.com/Sicut_study/items/aad3c38ab0fd1df894b2
の記事の連続的再学習というところを読んで思いついた。
問題と答えを登録し、問題のほうが LINE に送られてくる。
答えを入力できれば正解、答え以外であれば間違い + 正しい答えが送られてくる。
これで連続的再学習 (アクティブリコールと分散学習を組み合わせられる)