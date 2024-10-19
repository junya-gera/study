---
title: "Cloudflare Pages で Next.js の静的サイトをデプロイすると「Error: Output directory "out" not found.」と出る"
date: 
author: junya-gera
tags: [Next.js, Cloudflare Pages]
description: ""
---

こんにちは、じゅんじゅんです。

Cloudflare Pages を使い、 Next.js で作成した静的サイトをデプロイしようとしたところ、以下のエラーが発生しました。

```
Error: Output directory "out" not found.
```

## 原因

エラー文のとおり、静的ファイルをアウトプットするための `out` というディレクトリがないことが原因です。

Next.js のアプリを静的サイトとしてエクスポートするには、 `next.config.js` に以下の設定が必要です。

```js:title=next.config.js
const nextConfig = {
  output: "export",
};
```

上記により出力モードが静的エクスポートとなり、ビルド時に HTML・CSS・JS の静的ファイルを含んだ `out` ディレクトリが作成されます。

原因はこれだけで特に Cloudflare Pages を使っているからというわけではありませんが、せっかくなのでデプロイするところまでの手順を紹介します。

## Next.js のセットアップ

まずは Next.js のセットアップを行います。以下のコマンドを実行してください。ディレクトリ名は `cloudflare-pages-test` としました。

`npx create-next-app@latest`

Cloudflare Pages にホスティングする際に GitHub のリポジトリが必要になるので作成しておきます (作成手順は割愛します)。

`page.tsx` の内容はとりあえず以下のようにしておきます。

```js:title=page.tsx
export default function Home() {
  return (
    <main className="p-24">
      <div className="text-2xl">
        cloudflare test
      </div>
    </main>
  );
}
```

## Cloudflare のアカウント作成

[Cloudflare のサインアップ画面](https://dash.cloudflare.com/sign-up)にアクセスし、メールアドレス、パスワードを入力してサインアップします。

画像1

入力したメールアドレス宛に認証メールが届くので、有効化します。

## Pages の作成

アカウントが作成できたら以下の画面が開くので、「Start building」をクリックします。

画像2

「Workers & pages」を開きます。

画像3

「Pages」タブを選択し、「Git に接続」をクリックします。

画像4

「GitHub」タブの「GitHub に接続」をクリックします。

画像5

GitHub のアカウントを選んだら「Select repositories」をクリックし、先ほど作成したリポジトリを選択します。選択できたら「Save」をクリックします。

画像6

Pages に戻り、「セットアップの開始」をクリックします。

画像7

