4/13(Satur)
英単語アプリの差分を見ると、 Github のシークレット ID を記載した .env をプッシュしてしまっていることに気づいた。

以下に記載した方法を実行。そしたらプルリクが閉じられた。履歴も消えているので目的は果たしたはず。
https://zenn.dev/rabbit/articles/64957d0412ee2e

その後、変更がトラックできていないようでコミットできなくなっていた。
フォルダごと別のところにコピー、ブランチを変える、変えたブランチにコピーしたフォルダを貼り付けし、
差分をコミットできるようにした。

が、Github のプルリク作成画面にいってもプルリク作成のボタンが出ない。
↓ ここの状態。
https://blog.imanect.com/262-2/


作業ブランチで git pull origin main --allow-unrelated-histories を実行したら、以下のように表示された。
```
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint: 
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint: 
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.
```

GPT に聞いた。
このメッセージは、ローカルのブランチとリモートのブランチで分岐が発生しており、それらをどのように調整するかを指定する必要があることを示しています。分岐が発生する原因としては、--allow-unrelated-historiesフラグが使用されたことが挙げられます。このフラグは、ローカルとリモートの履歴が関連付けられていない場合にマージを許可しますが、その結果、分岐が発生する可能性があります。

メッセージは3つのオプションを提供しています:

git config pull.rebase false: マージを使用して分岐を調整します。
git config pull.rebase true: リベースを使用して分岐を調整します。
git config pull.ff only: ファストフォワードを使用して分岐を調整します。

以下のように実行した。
git config pull.rebase false
git pull origin main --allow-unrelated-histories

そしたらコンフリクトの差分がいっぱい出た。
全部入力のほうを適用し、プッシュしたらプルリクが作成できるようになった。
main ブランチと作業ブランチの履歴が異なっていたため、マージできなくなっていたらしい。
