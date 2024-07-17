### Next.js のプロジェクト全体に Tailwind CSS で Noto Sans JP を適用させる方法

↓ 方法はこれのとおりにすればいい
https://zenn.dev/takna/articles/next-tailwind-googlefonts-basic

1. `layout.tsx` で Noto Sans JP をインポート

```ts
import { Noto_Sans_JP } from "next/font/google";
```

2. Noto Sans JP のインスタンスを作成

```ts
const notoSansJP = Noto_Sans_JP({
  subsets: ['latin'],
  variable: '--font-noto-sans-jp',
  // weight: 'variable', // default なので不要。バリアブルフォントでなければ必要
  // display: 'swap', // default なので不要
  // preload: true, // default なので不要
  // adjustFontFallback: true, // next/font/google で default なので不要
  // fallback: ['system-ui', 'arial'], // local font fallback なので不要
})
```

バリアブルフォント: 幅、太さ、スタイルごとに個別のフォントファイルを用意するのではなく、書体のさまざまなバリエーションを 1 つのファイルに組み込むことができる OpenType フォント仕様の進化版

https://developer.mozilla.org/ja/docs/Web/CSS/CSS_fonts/Variable_fonts_guide

バリアブルフォントであれば weight は必要ない。next/font の公式にも書かれていた。
https://nextjs.org/docs/pages/building-your-application/optimizing/fonts