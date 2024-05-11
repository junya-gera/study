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

4/28(Sun)
Workers の Cron Triggers について調べる。
https://zenn.dev/toraco/articles/55f359cbf94862

ダッシュボードの Cron イベントというところを見ると毎分実行されていた！
最初にデプロイしてしまったからか。3日間実行されっぱなしだった。

wrangler.toml を以下のようにして cron を止める。
```
[triggers]
crons = []
```

npx Wrangler deploy コマンドを実行してデプロイしようとしたが、以下のエラーが表示された。
Missing entry-point: The entry-point should be specified via the command line (e.g. `wrangler deploy path/to/script`) or the `main` config field.

entry-point は wrangler.toml の main = "src/index.ts" を見ている。
実行しているディレクトリがひとつ上だったため、 src/index.ts がなかっただけだった。
これでデプロイは成功したが、なぜかトリガーが削除されなかった。

ダッシュボードの「設定」→「トリガー」から cron の設定が表示されているところの点3つをクリックすると削除ができるのでそれで削除した。
画面から Cron イベントがなくなっていた。

とにかくこれで定期実行できることがわかったので、 Google フォトから画像を取ってくるのとそれを R2 に保存する方法を調べる。

5/8(Wed)
https://developers.cloudflare.com/r2/get-started/
の公式を参考に、 R2 のバケットを作成する。
クレジットカードを登録しないといけないので登録した。

R2 の前に Google photos api が先かと思うので、
https://qiita.com/doran/items/8695e6007765ab5b382c
https://qiita.com/doran/items/15b2c59adb410ddeeb8a#13-api%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B%E3%81%9F%E3%82%81%E3%81%AE%E3%82%AD%E3%83%BC%E5%8F%96%E5%BE%97
https://developers.google.com/photos/library/guides/get-started?hl=ja
の記事を参考にまずは API を使える状態にする。

「承認済みの JavaScript 生成元」がよくわからなかったが、とりあえず https://edcd4bde.cloudflare-jw-test.pages.dev にしておいた。
これで必要な ID は作成できた。これらが書かれている json をダウンロードしておいた。

ChatGPT が作成してくれたソースコードで試してみる。
R2 がまだない。

wrangler r2 bucket create photosTest
このコマンドを実行してバケットを作ろうとしたが、
The specified bucket name is not valid. [code: 10005]
と表示される。

https://github.com/cloudflare/workers-sdk/issues/3227
を見ると大文字やハイフンが使えないらしい。全部小文字にしたら作成できた。

workers に以下を追加。アカウント ID は R2 の画面の右上に書いてあった。
account_id = 
workers_dev = true

google photos library API を使って画像を取ってくる部分の検証。
https://developers.google.com/photos/library/guides/list?hl=ja
の REST を見ると、 oauth2-token というものが必要だが、これがどこにあるかわからない。

https://zenn.dev/ficilcom/articles/f518ce531b2d42
https://qiita.com/Cheap-Engineer/items/f849b9b1c2a4da9e25c1
この辺を見ると、ブラウザである URL を入れるとトークンがわかるらしい。

```js
function getAuthUrl() {
  const authUrl = 'https://accounts.google.com/o/oauth2/v2/auth';
  const clientId = '1234hoge-fuga.apps.googleusercontent.com';
  const redirectUri = 'https://hogefuga.co.jp/L/exec';
  const scopes = ['https://www.googleapis.com/auth/drive','https://www.googleapis.com/auth/spreadsheets','https://www.googleapis.com/auth/script.external_request'];

  //スペース区切りでJOIN
  const scope = scopes.join(' ');

  //認証コード取得用URL生成
  const requestUrl = `${authUrl}?scope=${scope}&access_type=offline&prompt=consent&include_granted_scope=true&response_type=code&redirect_uri=${redirectUri}&client_id=${clientId}`;
  Logger.log(requestUrl);
}
```

clientId はダウンロードした JSON にある。
scopes は https://www.googleapis.com/auth/photoslibrary.readonly だけでいいと思われる。
redirectUri はなに？

Google API の認証情報の画面で OAuth 2.0 クライアント ID とサービスアカウントというものが選択できるが、
これらの違いを ChatGPT に聞いてみたところ、
OAuth 2.0 クライアント ID はユーザー認証、サービスアカウントはサービス間通信に使用されるものらしい。
そのため、 workers ではサービスアカウントのほうを使用すると思われる。

以下にサービスアカウントを使用して workers で google api を使用するソースコードが書かれていた。
https://github.com/Schachte/cloudflare-google-auth
↑
嘘だった。ユーザーデータにアクセスする際は OAuth 2.0 クライアント ID でないといけないらしい。

リダイレクト URI は workers のルートになっていた https://long-heart-c221.aa106921.workers.dev/ にした。
これで上に書いた js で作られた URL をブラウザで確認。
google のアカウントを聞かれたのでおこげりんを選択。

https://long-heart-c221.aa106921.workers.dev/ の code= の後にトークンらしき文字列が記載されていた。


取得した認証コードを使用して、以下のコマンドをコマンドプロンプトで実行。
https://blog.shinonome.io/google-api/ を参考にした。

```
curl -X POST \
     --data "code=手順2で取得した認可コード" \
     --data "client_id=クライアントID" \
     --data "client_secret=クライアントシークレット" \
     --data "redirect_uri=リダイレクトURI" \
     --data "grant_type=authorization_code" \
     https://accounts.google.com/o/oauth2/token
```

アクセストークンを取得できた。
```
{
  "access_token": "hogehoge",
  "expires_in": 3599,
  "refresh_token": "fugafuga",
  "scope": "https://www.googleapis.com/auth/photoslibrary.readonly",
  "token_type": "Bearer"
}
```


5/9(Thurs)
なんとかエラーなく処理を書き、npx wrangler deploy でデプロイし、ダッシュボードのリアルタイムログで確認。
scheduled() がないと言われた。

{
  "outcome": "exception",
  "scriptVersion": {
    "id": "a18c36c3-0e56-4f3f-9bc9-37d0f4531f51"
  },
  "scriptName": "long-heart-c221",
  "diagnosticsChannelEvents": [],
  "exceptions": [
    {
      "name": "Error",
      "message": "Handler does not export a scheduled() function",
      "timestamp": 1715230200675
    }
  ],
  "logs": [],
  "eventTimestamp": 1715230200665,
  "event": {
    "cron": "*/5 * * * *",
    "scheduledTime": 1715230200000
  },
  "id": 0
}

```ts
// eslint-disable-next-line import/no-anonymous-default-export
export default {
  async scheduled(event: ScheduledEvent, env: Env, ctx: ScheduledEvent) {
    ctx.waitUntil(putImage(env));
  },
};
```

こうするとログでは Ok となるが、 R2 に何も保存されていない。

手元の js ファイルにこの処理を移し、実行してみると 401 になっていた。
トークンを作成し直して、 mediaItems の API を実行してみたところ、 mediaItem の後の id を指定する部分に 1 と入れていたが
これは間違いで、ランダムな文字列が id になっているようだった。
適当に id を選んで API に入れて workers を動かしたところ、 R2 に画像が保存されていた。

その後、 album を取得する API を実行し、その album 内の最新5件の画像を保存するよう修正できた。
