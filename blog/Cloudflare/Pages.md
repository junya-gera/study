4/23(Tues)
Pages で Next.js の静的サイトデプロイ、Workers で Google フォトから画像を取得し、 R2 に保存してホストできるかを試す。

まずはテスト用の Next の静的 Web サイトを作る。
WSL に構築し、 Git のリポジトリを作る。

Pages でのデプロイを試す。以下の記事を参考にする。
https://zenn.dev/rivine/articles/2023-06-23-deploy-hugo-to-cloudflare-pages

Workers はここを参考にする。
https://zenn.dev/moutend/articles/97c98a277f4bae

この記事によれば、 Workers は軽量の処理には向いているが、メモリ制限があるため、画像処理は不向きらしい。
一度試してみて問題ないか確認する。

Git リポジトリを作ったので、 Pages でデプロイを試してみたが、以下のエラーになる。
Error: > Couldn't find any `pages` or `app` directory. Please create one under the project root

cloudflare-test/my-app/app になっているが、 my-app からにする必要があったかもしれない。
いったん Git リポジトリを作り直す。

main ブランチと master ブランチでややこしくなったがなんとか作り直した。
ローカルを master から main ブランチに変更
https://qiita.com/fk_chang/items/a4839a595fef9a2c3724

これでデプロイを試したが以下のエラー。
Error: Output directory "out" not found.

https://qiita.com/tatmius/items/ac8352587b7707c031d8
に書いてあった next.config.js の修正をしたが一緒だった。

4/24(Wed)
https://community.cloudflare.com/t/cloudflare-pages-build-not-finding-output-directory/310525
や、 GPT の言う通り、 package.json に next export を追記した。

```json
"build": "next build && next export",
```

4/25(Thurs)
next.config.mjs に以下の2つが書かれていた。 Common.js の書き方の 1 を削除したらデプロイが完了した。
1. module.exports = nextConfig;
2. export default nextConfig;

https://edcd4bde.cloudflare-jw-test.pages.dev/
でアクセスすることができた。

workers の使い方を調べているがいまいちわからない。
とりあえず以下の記事を参考に CLI で作ってみる。
https://zenn.dev/moutend/articles/97c98a277f4bae

どのタイプの workers にするかみたいなのが聞かれて、 cron trigger というのがあったので選択した。
https://developers.cloudflare.com/workers/configuration/cron-triggers/ を見ると、
Cron Triggers are ideal for running periodic jobs, such as for maintenance or calling third-party APIs to collect up-to-date data.
と書かれてあったので定期的に Google フォトから画像を取ってくるのにぴったりかもしれない。
スケジュールの設定は wrangler.toml というファイルに記述するらしい。
公式を見てると日次実行はできそうな感じがする。

なぜかデプロイがタイムアウトになっており画面には Error 1101 と表示される。
どっちかというとこっちが問題な気がする。
→ いや、 cloudflare のダッシュボードには作成されていた。 Web サイトではないので画面で開けないのは問題ないのかも。

```
├  SUCCESS  View your deployed application at https://long-heart-c221.aa106921.workers.dev
│ 
│ Navigate to the new directory cd long-heart-c221
│ Run the development server npm run start
│ Deploy your application npm run deploy
│ Read the documentation https://developers.cloudflare.com/workers
│ Stuck? Join us at https://discord.cloudflare.com
│ 
├ Waiting for DNS to propagate 
│ DNS propagation complete.
│ 
├ Waiting for deployment to become available 
│ timed out while waiting for https://long-heart-c221.aa106921.workers.dev - try accessing it in a few minutes.
│ 
╰ See you again soon! 
```

まずは数秒おきに console.log でも定期実行を試してみる。
それができたら Google Photos API の使い方を調べて画像を取ってこれるようにする。