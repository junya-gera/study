05/23(Thurs)

テキストを音声に変換してアプリ上で再生する方法 (AWS Polly・Web Speech API)



こんにちは、じゅんじゅんです。

先日、社内の勉強会で開発している英単語暗記アプリに英語の音声を再生する機能を追加しました。その方法について紹介します。

実装は以下のようにしました。

1. AWS Polly のテキストを音声に変換する機能を使って音声データを返す Lambda 関数を Serverless Framework で作成
2. 1 の Lambda の関数 URL を使用してアプリから Lambda 関数を呼び出し、取得した音声データを再生

以前[記事](https://mseeeen.msen.jp/how-to-execute-lambda-from-line-using-function-urls/)にした Lambda 関数 URL を使って

最後に AWS Polly ではなく Web Speech API を使って音声再生機能を実装する方法についても紹介しています。

## AWS Polly とは

## Web Speech API で音声再生機能を実装

同様の機能を Web Speech API を使って実装します。