6/20(Thurs)
適切な画像を配置するのに苦戦。
Tailwind の幅の選択肢にデザインと同じぐらいの幅がなかったので、
`h-[340px]` というように直接値を設定した。

pdf のデザインの画像の縦横のピクセルを「シンプル画面定規」というアプリで測ったあと、
GIMP の切り抜きでその比率のサイズに切り抜く (比率は手で計算)。
その後「画像」→「画像の拡大・縮小」で適切な比率にする。
「ファイル」→「○○に上書きエクスポート」で保存。

これでなんとかなるはず。

7/8(Mon)
Google Map を HP に表示。

↓ Google Cloud Platform はこれを参考
https://zenn.dev/kou_kawa/articles/11-next-ts-googlemap

↓ Next.js での実装はこれを参考
https://qiita.com/kccs_kazuo_fukudome/items/6e279558037105d2090c

7/12(Fri)
静的ホスティングサービスによりデプロイを行うため、 next.config.mjs を以下のようにし、
Next.js を静的サイトジェネレーターとして使用するようにした (デフォルトでは SSR になっている) 。

```js
const nextConfig = {
  output: "export",
};

export default nextConfig;
```

これをすると、 next/Image の最適化が行われず、以下のエラーが発生する。
```
error: image optimization using the default loader is not compatible with `{ output: 'export' }`.
```

静的サイトにすると最適化できなくなるらしい。 next.config.mjs を以下のように修正して最適化を無効化したらエラーが消えた。

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: "export",
  images: {
    unoptimized: true,
  },
};

export default nextConfig;
```

