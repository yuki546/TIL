# useContext

## 概要

`useContext` は React のフックで、**コンポーネントツリー全体でデータを共有する** ためのもの。
親から子へ props を一つひとつ渡していく必要がなくなる。

主な用途：
- テーマ（ダーク / ライトモード）
- ログイン中のユーザー情報
- 言語設定（i18n）
- グローバルな設定値

---

## なぜ必要か？（Props バケツリレー問題）

深くネストしたコンポーネントに値を届けるには、途中のコンポーネントを**中継**し続ける必要がある。
これが **「Props バケツリレー（prop drilling）」** と呼ばれる問題。

```
App（user を持つ）
  └─ Layout（user を中継するだけ）
       └─ Header（user を中継するだけ）
            └─ UserIcon（ここで user を使いたい！）
```

Context を使うと、途中を経由せずに**必要なコンポーネントが直接値を受け取れる**。

---

## 基本の使い方

### 1. Context を作る

```tsx
import { createContext } from 'react';

const ThemeContext = createContext('light'); // 引数はデフォルト値
```

### 2. Provider で値を渡す

`Provider` で囲んだ範囲に値が届く。

```tsx
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Layout />
    </ThemeContext.Provider>
  );
}
```

### 3. useContext で受け取る

```tsx
import { useContext } from 'react';

function UserIcon() {
  const theme = useContext(ThemeContext); // "dark"
  return <div className={theme}>👤</div>;
}
```

途中の `Layout` や `Header` が `theme` を中継する必要はない。

---

## state と組み合わせる

値を更新もしたい場合は、`useState` と組み合わせて **値と更新関数をまとめて** 渡すのが定番。

```tsx
const ThemeContext = createContext(null);

function App() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Layout />
    </ThemeContext.Provider>
  );
}

// 子コンポーネント側
function ThemeButton() {
  const { theme, setTheme } = useContext(ThemeContext);
  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      現在: {theme}
    </button>
  );
}
```

---

## よくあるミス・注意点

- **Provider で囲み忘れる**：Provider の外で `useContext` すると、`createContext` に渡した**デフォルト値**が使われる（意図しない挙動になりがち）
- **value にオブジェクトを毎回新しく渡す**：Provider が再レンダリングされるたびに `value={{...}}` が新しい参照になり、**受け取る側すべてが再レンダリング**される → 必要に応じて `useMemo` で安定させる
- **何でも Context に入れる**：Context の値が変わると、それを使う全コンポーネントが再描画される。頻繁に変わる値を大きな範囲で共有すると重くなる
- **状態管理ライブラリの代わりに使いすぎる**：複雑な状態管理は Redux / Zustand などの方が向く場合もある

---

## useState / useRef との違い

| フック | 役割 |
| --- | --- |
| [useState](useState.md) | コンポーネント内の状態を持つ |
| [useRef](useRef.md) | 再描画不要な値・DOM 参照を持つ |
| useContext | ツリー全体でデータを共有する |

---

## 参考

- [React 公式ドキュメント - useContext](https://react.dev/reference/react/useContext)
- [Passing Data Deeply with Context](https://react.dev/learn/passing-data-deeply-with-context)
