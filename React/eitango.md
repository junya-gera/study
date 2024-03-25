2024/03/23(Satur)
直接飛んでほしいページは URL を設定
そうでなければ URL は使わない

前にやったことの記録やソースにコメントを残していないので思い出さないといけない。
作業記録はつける。

↓ 前回のメモ
---
test/setting と test/result はやめて、 /test のコンポーネントとして扱う。

1. TestSetting から Test に showSetting を true にすることとどんな設定かを渡す。
2. 設定のとおりの単語一覧を取得し、 TangoTest に切り替わる。
3. テストが終わったら showResult を true にし、 TestResult を表示。

とりあえず TestSetting から showSetting を true にすることはできたはず。
---

残りタスク
- 〇リザルトの覚えた件数が1ずれる
- 〇覚えた数によってリザルトに表示する文字を変更する
- 単語追加・編集画面の品詞のラベルの位置がおかしい
- 〇テスト設定で入力した内容をテストに反映させる
- テストする単語をランダムにし、設定した数だけ出題されるようにする

### リザルトの覚えた件数が1ずれる
最初の1問目で「覚えた」を押したときに 0 になっている。
これが解決できたら記事にできるか？

一応解決した。
以下のようにしたとき、最初の1回目の console になぜか 1 ではなく 0 と表示される。

```ts
  const updateIsPassedAndChangeNextTango = (isPassed: Boolean) => {
    updateIsPassed(props.tangos[currentIndex].id, isPassed);

    if (isPassed) {
      setPassCount((prevPassCount) => prevPassCount + 1);
    }
    console.log(`passCount: ${passCount}`);
    changeNextTango();
  };
```

そのため最初の1回目が問題かと思っていたが、以下の記事を見ると console に表示されるのが古くなるのは仕様で、
実際の state は更新されているらしい (実際画面に表示した passCount は正しい) 。

https://zenn.dev/takuty/articles/c032310a6643ac
→ 参考資料に mseeeen の記事が！
https://qiita.com/honey32/items/ee8d1577e68b0d58678d

qiita のほうを読むと、console の表示が古いのは setState が非同期処理だからというのは関係がなく、
 JS のクロージャという仕様が関係している。
関数はその関数の外側にある変数の値をキャプチャするため、関数内で +1 したとしても、
関数内で参照するのはその処理が行われる前の状態の変数。

という JS の仕様から、 React の State は「最新の state は次のレンダリングのときにしか反映されないようになる」
という仕様になる。

そのため、原因は最後リザルトに passCount を渡す際そのまま passCount を渡していることだった。
+1 した値を渡すようにすると解決。

```ts
  const updateIsPassedAndEndTest = (isPassed: Boolean) => {
    updateIsPassed(props.tangos[currentIndex].id, isPassed);
    if (isPassed) {
      setPassCount((prevPassCount) => prevPassCount + 1);
    }
    console.log(`passCount: ${passCount}`);
    setCurrentIndex(0);
    props.showResult(passCount);
  };
```

↓ 最終的なコード

```ts
  const updateIsPassedAndChangeNextTango = (isPassed: Boolean) => {
    updateIsPassed(props.tangos[currentIndex].id, isPassed);
    if (isPassed) {
      setPassCount((prevPassCount) => prevPassCount + 1);
    }
    changeNextTango();
  };

  const updateIsPassedAndEndTest = (isPassed: Boolean) => {
    updateIsPassed(props.tangos[currentIndex].id, isPassed);
    // 最後の問題の場合は結果画面に遷移
    if (isPassed) {
      setCurrentIndex(0);
      // setPassCount((prevPassCount) => prevPassCount + 1);
      // とすると、レンダリング前の passCount を渡してしまうため、 1 少なくなってしまう
      props.showResult(passCount + 1);
    } else {
      setCurrentIndex(0);
      props.showResult(passCount);
    }
  };
  ```

3/24(Sun)
次はテスト設定を反映させる。
まずはまだ覚えていない単語に絞って出題されるようにする。
TestSetting から StartTest() を呼んでいるので、ここに設定内容を引数で渡す。

setState で isOnlyUnpassed を Boolean で管理。
未暗記のみ出題のチェックボックスを以下のようにし、 isOnlyUnpassed に反映させるようにした。
```ts
<h2>未暗記のみ出題</h2>
<Checkbox
  inputProps={{ "aria-label": "controlled" }}
  onChange={handleCheckboxChange}
  checked={isOnlyUnpassed}
/>
```

そのうえで、 startTest に引数で isOnlyUnpassed を渡すようにした。
無名関数を使うと引数を渡せた。

```ts
<Button
  variant="contained"
  size="large"
  onClick={() => startTest(isOnlyUnpassed)}
>
  テスト開始
</Button>
```

次にチェックされた品詞もクエリパラメータで渡せるようにする。
以下のようにして condition という JSON 文字列を渡す。

```ts
  // テスト用の単語を取得
  const fetchTestTango = async (
    categories: String[],
    isOnlyUnpassed: Boolean
  ) => {
    const condition = {
      isOnlyUnpassed,
      categories,
    };
    const res = await fetch(
      `/api/test?condition=${JSON.stringify(condition)}`,
      {
        cache: "no-store",
      }
    );
// 以下略
```

```ts
// 2. 単語一覧を取得するAPI
export async function GET(req: NextRequest) {
  // クエリパラメータを変数に格納
  const conditionsJson = req.nextUrl.searchParams.get('condition');
    // JSONをパースしてオブジェクト配列に変換
    const condition: Condition = conditionsJson ? JSON.parse(conditionsJson) : [];
// 以下略
```

TestSetting からチェックが入った品詞を渡す。
CategoryCheckBoxes コンポーネントから onCheckeboxChange 関数を TestSetting に渡し、チェックが入った品詞を都度 state で持つようにした。

categories と isOnlyUnpassed を startTest 関数で Test コンポーネントに渡し、 fetchTestTango 関数で condition という JSON 文字列にし、クエリパラメータにして API に渡す。

API 側でオブジェクト配列に変換し、 prisma の findMany の where に設定する。

以上の内容で記事が書けるかもしれない。