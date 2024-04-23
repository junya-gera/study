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