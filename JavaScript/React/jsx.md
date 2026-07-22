# JSX

## 概要

JSX（JavaScript XML）は、**JavaScript の中に HTML のような記述を書ける構文**。
React でコンポーネントの見た目（UI）を書くときに使う。

```tsx
const element = <h1>Hello, React!</h1>;
```

一見すると HTML だが、これは JavaScript の拡張構文。
最終的にはビルド時に**通常の JavaScript の関数呼び出しに変換**される。

---

## JSX は最終的に関数呼び出しになる

JSX はそのままブラウザで動くわけではなく、内部的には `React.createElement`（React 17 以降は専用の関数）に変換される。

```tsx
// 書いているコード
const element = <h1 className="title">Hello</h1>;

// 変換後（イメージ）
const element = React.createElement('h1', { className: 'title' }, 'Hello');
```

> つまり JSX は「HTML を書いている」のではなく、「JavaScript のオブジェクト（React 要素）を作る糖衣構文（シンタックスシュガー）」だと捉えると理解しやすい。

---

## JSX の中に JavaScript を埋め込む

`{}` で囲むと、JavaScript の式を埋め込める。

```tsx
const name = 'taro';
const element = <h1>Hello, {name}!</h1>; // Hello, taro!
```

- 埋め込めるのは**式（値になるもの）**：変数、計算、関数呼び出し、三項演算子など
- **文（if 文、for 文）は直接は書けない**

```tsx
// OK：三項演算子（式）
<p>{isLogin ? 'ようこそ' : 'ログインしてください'}</p>

// NG：if 文（文）はそのまま書けない
<p>{if (isLogin) {...}}</p>
```

---

## HTML との違い（よくハマる点）

JSX は HTML に似ているが、JavaScript なので細かい違いがある。

| HTML | JSX | 理由 |
| --- | --- | --- |
| `class` | `className` | `class` は JS の予約語のため |
| `for` | `htmlFor` | `for` は JS の予約語のため |
| `onclick` | `onClick` | イベントは**キャメルケース** |
| `style="color:red"` | `style={{ color: 'red' }}` | style は**オブジェクト**で渡す |

```tsx
<button className="btn" onClick={handleClick} style={{ color: 'red' }}>
  クリック
</button>
```

---

## ルールいくつか

### 1. 要素は 1 つにまとめる必要がある

複数の要素を返すときは、1 つの親でくくる。
無駄な `<div>` を増やしたくないときは **フラグメント（`<>...</>`）** を使う。

```tsx
// NG：複数要素をそのまま返せない
return (
  <h1>タイトル</h1>
  <p>本文</p>
);

// OK：フラグメントでまとめる
return (
  <>
    <h1>タイトル</h1>
    <p>本文</p>
  </>
);
```

### 2. タグは必ず閉じる

`<img>` や `<br>` のような要素も、JSX では**自己終了**が必要。

```tsx
<img src="..." />
<br />
```

### 3. リストには key が必要

配列から要素を作るときは、各要素に一意な `key` を付ける（React が差分検出に使う）。

```tsx
{users.map((user) => (
  <li key={user.id}>{user.name}</li>
))}
```

---

## 条件分岐・繰り返しの書き方

JSX 内では文が書けないので、式で表現する。

```tsx
{/* 条件付き表示（&& を使う） */}
{isLogin && <p>ログイン中</p>}

{/* 三項演算子 */}
{isLogin ? <Dashboard /> : <Login />}

{/* 繰り返し（map） */}
{items.map((item) => <li key={item.id}>{item.name}</li>)}
```

---

## まとめ

- JSX は **JavaScript の中に UI を宣言的に書ける構文**
- 実体は `React.createElement` への糖衣構文で、**HTML ではなく JavaScript**
- `{}` で式を埋め込める（文は書けない）
- `class → className` など HTML との差分や、`key` / フラグメントなどのルールがある

「HTML を書いている」のではなく「JS で UI オブジェクトを組み立てている」と理解すると、条件分岐や `{}` の使い方がしっくりきた。

---

## 参考

- [React 公式ドキュメント - Writing Markup with JSX](https://react.dev/learn/writing-markup-with-jsx)
- [JavaScript in JSX with Curly Braces](https://react.dev/learn/javascript-in-jsx-with-curly-braces)
