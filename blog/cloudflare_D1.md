---
title: "Cloudflare Workers・D1・Hono でリマインダー LINE Bot を作りました"
date: 
author: junya-gera
tags: [Cloudflare Workers, Cloudflare D1, Hono]
description: ""
---

こんにちは、じゅんじゅんです。

## プロジェクト作成

まずは Next.js のセットアップを行います。以下のコマンドを実行してください。ディレクトリ名は `forgetting-curve` としました。

`npx create-next-app@latest`

`forgetting-curve` 下で以下のコマンドを実行し、 Hono のプロジェクトも作成します。

`npm create hono@latest`

「? Which template do you want to use?」という質問では「cloudflare-worker」を選択します。

次に、以下のコマンドで Cloudflare Workers の管理ツールである `wrangler` をインストールします。

`npm i -D wrangler`


