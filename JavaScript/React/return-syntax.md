# return の書き方（`{}` / `()` / どちらも使わない の違い）

## 概要

コンポーネントを書いていると、次のように書き方がバラバラに見えることがある。

```tsx
// ① {} と return
const Hello = () => {
  return <p>Hello</p>;
};

// ② () だけ（return がない）
const Hello = () => (
  <p>Hello</p>
);

// ③ どちらも使わない
const Hello = () => <p>Hello</p>;
```

3 つとも**同じ結果**になる。違いは次の 2 つの仕組みから来ている。

- `{}` → **アロー関数の「ブロック本体」**。中に処理を書けるが、`return` が必要
- `()` → **JSX を複数行で書くためのカッコ**。JSX 専用の構文ではなく、ただの「式をくくるカッコ」

これは React ではなく、JavaScript（アロー関数）の仕組み。

> JSX の中で使う `{}`（`<p>{name}</p>`）は、JS の式を埋め込むためのもので、ここでの `{}` とは**別物**。

---

## アロー関数の 2 つの書き方

アロー関数の `=>` の後ろには、**ブロック**か**式**のどちらかを書ける。

### ブロック本体（`{}` あり）→ `return` が必要

```tsx
const add = (a: number, b: number) => {
  return a + b;
};
```

`{}` の中は普通の関数と同じで、**`return` を書かないと `undefined` が返る**。

### 式本体（`{}` なし）→ `return` 不要（暗黙の return）

```tsx
const add = (a: number, b: number) => a + b;
```

`=>` の後ろの式が**そのまま戻り値**になる。

---

## `()` は何のため？

`()` は「ここからここまでが 1 つの式」と示すためのカッコ。
`const sum = (1 + 2);` と `const sum = 1 + 2;` が同じ意味になるように、**式全体をくくるカッコは付けても付けなくても値は変わらない**。

JSX で `()` を使う理由は、**JSX を改行して書きたいから**。

### 式本体で改行したいとき

```tsx
// OK：1 行なら () はいらない
const Hello = () => <p>Hello</p>;

// OK：複数行にするなら () でくくると読みやすい
const Card = () => (
  <div>
    <h1>タイトル</h1>
    <p>本文</p>
  </div>
);
```

### `return` の後ろで改行したいとき（ここが重要）

```tsx
// NG：return の直後で改行すると undefined が返る
const Card = () => {
  return
    <div>
      <p>本文</p>
    </div>;
};

// OK：return と同じ行で ( を開く
const Card = () => {
  return (
    <div>
      <p>本文</p>
    </div>
  );
};
```

JavaScript には**自動セミコロン挿入（ASI）**という仕組みがあり、`return` の直後に改行があると `return;` と解釈されてしまう。
`return (` と同じ行でカッコを開けば、「式がまだ続いている」ことが伝わるので安全。

> つまり `return (...)` の `()` は、「`return` の後ろで改行しても大丈夫にするためのカッコ」。

---

## どれを使えばいい？

| 書き方 | 使う場面 |
| --- | --- |
| `() => <p>...</p>` | JSX を返すだけで、**1 行に収まる** |
| `() => ( ... )` | JSX を返すだけで、**複数行になる** |
| `() => { ...; return ( ... ); }` | **return の前に処理がある**（Hooks、変数、if 文など） |

return の前に処理を書きたいなら、`{}`（ブロック本体）一択。

```tsx
const Counter = () => {
  const [count, setCount] = useState(0); // ← 処理がある

  if (count > 10) {
    return <p>上限です</p>; // 1 行なら () はなくても OK
  }

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
};
```

---

## よくあるミス

### 1. `{}` で書いたのに `return` を忘れる

```tsx
// NG：何も表示されない
{items.map((item) => {
  <li key={item.id}>{item.name}</li>;
})}

// OK：return を書く
{items.map((item) => {
  return <li key={item.id}>{item.name}</li>;
})}

// OK：() にして暗黙の return にする
{items.map((item) => (
  <li key={item.id}>{item.name}</li>
))}
```

`map` のコールバックだと**エラーにならず、何も表示されないだけ**なので気づきにくい。
`() => {` と `() => (` は見た目が似ているので要注意。

### 2. オブジェクトを返したいのに `{}` がブロック扱いされる

```tsx
// NG：{} がブロックと解釈され、undefined が返る
const toUser = (name: string) => { name: name };

// OK：() でくくると「これはオブジェクト（式）」と伝わる
const toUser = (name: string) => ({ name: name });
```

`=>` の直後の `{` は**必ずブロック**として扱われる。
オブジェクトを式本体で返したいときは `({ ... })` と書く。

---

## まとめ

- `{}` → **ブロック本体**。処理を書けるが `return` が必要
- `{}` なし → **式本体**。式がそのまま返る（暗黙の return）
- `()` → **式をくくるだけのカッコ**。JSX を複数行で書くときに使う
- `return` の直後で改行すると `undefined` になるので、`return (` と同じ行でカッコを開く
- オブジェクトを式本体で返すときは `({ ... })`

「`{}` は関数の中身、`()` はただのカッコ」と分けて考えると、3 パターンの違いがすっきりした。

---

## 参考

- [MDN - アロー関数式](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [MDN - return（自動セミコロン挿入）](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/return#%E8%87%AA%E5%8B%95%E3%82%BB%E3%83%9F%E3%82%B3%E3%83%AD%E3%83%B3%E6%8C%BF%E5%85%A5)
- [React 公式ドキュメント - Your First Component](https://react.dev/learn/your-first-component)
