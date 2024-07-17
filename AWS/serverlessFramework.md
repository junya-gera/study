6/22(Satur)
Serverless Framework が v4 になっていろいろアップデートされているらしいので公式を読んで勉強する。

ESBuild という設定ができるようになっている。
これはこれまでは serverless-esbuild というプラグインで設定していたがこれがいらなくなったらしい。
https://www.serverless.com/framework/docs/providers/aws/guide/building

これまで使ったことはなかったが、便利そうなのでどんな設定ができるのか GPT に聞いて整理する。

↓ 公式に載っていた例
```yml
build:
  esbuild:
    # Enable or Disable bundling the function code and dependencies. (Default: true)
    bundle: true
    # Enable minifying function code. (Default: false)
    minify: false
    # NPM packages to not be bundled
    external:
      - @aws-sdk/client-s3
    # NPM packages to not be bundled, as well as not included in node_modules
    # in the zip file uploaded to Lambda. By default this will be set to aws-sdk
    # if the runtime is set to nodejs16.x or lower or set to @aws-sdk/* if set to nodejs18.x or higher.
    exclude:
      - @aws-sdk/*
    # The packages config, this can be set to override the behavior of external
    # If this is set then all dependencies will be treated as external and not bundled.
    packages: external
    # By default Framework will attempt to build and package all functions concurrently.
    # This property can bet set to a different number if you wish to limit the
    # concurrency of those operations.
    buildConcurrency: 3
    # Enable or configure sourcemaps, can be set to true or to an object with further configuration.
    sourcemap:
      # The sourcemap type to use, options are (inline, linked, or external)
      type: linked
      # Whether to set the NODE_OPTIONS on functions to enable sourcemaps on Lambda
      setNodeOptions: true
```

### minify
不要な空白やコメントの削除・変数名の短縮・コードの最適化を行い、
デプロイされるファイルサイズを小さくし、パフォーマンスを向上させる。

### external
バンドルしない NPM パッケージを指定する。

#### 指定しない場合
esbuild は全ての依存関係を関数コードと一緒にバンドルする。
これにはプロジェクト内でインストールされている全ての NPM パッケージが含まれる。

- メリット
  - 単一ファイルの利便性：すべての依存関係が1つのファイルにバンドルされるため、デプロイ時に単一ファイルをアップロードするだけで済みます。
  - 外部依存が不要：Lambda関数実行時に外部の依存パッケージを参照する必要がなく、依存パッケージの欠如によるエラーが防げます。
- デメリット
  - バンドルサイズが大きくなる：すべての依存関係を含めるため、バンドルファイルのサイズが大きくなります。
  - ビルド時間の増加：すべての依存関係をバンドルするプロセスがビルド時間を増加させる可能性があります。

#### 指定した場合
指定したパッケージはバンドルファイルに含まれず、 Lambda 関数が実行される環境で直接参照される。

- メリット
  - バンドルサイズの削減：指定した依存関係がバンドルに含まれないため、バンドルファイルのサイズが小さくなります。
  - ビルド時間の短縮：バンドルから除外されたパッケージがあるため、ビルド時間が短縮されます。
- デメリット
  - 依存パッケージの管理：バンドルに含まれないパッケージがLambda環境に存在することを確認する必要があります。存在しない場合、実行時エラーが発生する可能性があります。
  - ランタイム依存：特定のバージョンの依存パッケージがLambda環境に適切に配置されていることを前提とするため、依存関係のバージョン互換性に注意が必要です。

例えば `aws-sdk` は Lambda にデフォルトで提供されているため、
バンドルする必要がない。

### exclude
external はバンドルファイルからは除外するが node_modules には含まれていた。
exclude は node_modules にも含めない。

デフォルトで提供されている `aws-sdk` を exclude に設定することで
パッケージサイズを減少させる。

### packages
依存関係をどのように扱うかを制御する。
external 設定の動作を上書きする。
`packages: external` を設定すると、全ての依存関係がバンドルに含まれず、
実行環境でこれらの依存関係が利用可能であることを前提とする。
依存関係がバンドルに含まれないため、デプロイパッケージのサイズが大幅に小さくなる。
バンドルされないため、依存関係のバージョンや存在をLambda環境などで管理する必要があります。


serverless dev を試してみた。
エラーコードがローカルの VSCode 上に表示されるので、 CloudWatch Logs を見なくても済んで超便利。
↓ 仕組みはこれに詳しく書かれている。
https://yasuda.cloud/entry/2024/06/21/004752

Ctrl + C しても自動実行は続いていたので、 sls remove しないといけない。
(Removing "notify-unpassed-words" from stage "dev" と出る)