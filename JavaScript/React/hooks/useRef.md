# useRef

## 概要

`useRef` は React のフックで、**再レンダリングをまたいで値を保持する「箱」** を作るためのもの。
主な用途は次の 2 つ。

1. **DOM 要素への参照**を持つ（input にフォーカスを当てる、など）
2. **再レンダリングに影響しない値**を保持する（タイマー ID、前回の値など）

`useState` との一番の違いは、**ref の値を変えても再レンダリングが起きない**こと。

---

## 基本構文

```tsx
const ref = useRef(初期値);
```

- 返り値はオブジェクト `{ current: 初期値 }`
- 実際の値は `ref.current` で読み書きする
- `ref.current` を書き換えても**再レンダリングは発生しない**

```tsx
const countRef = useRef(0);

countRef.current += 1; // 値は更新されるが、画面は再描画されない
```

---

## 用途1：DOM 要素への参照

`ref` 属性に渡すと、その DOM 要素にアクセスできる。

```tsx
function SearchBox() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus(); // input にフォーカスを当てる
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>フォーカス</button>
    </>
  );
}
```

DOM の直接操作（フォーカス、スクロール、動画の再生など）はこのパターンを使う。

---

## 用途2：再レンダリングに影響しない値の保持

「保持はしたいが、変わっても再描画は不要」な値に使う。代表例はタイマー ID。

```tsx
function Timer() {
  const intervalRef = useRef<number | null>(null);

  const start = () => {
    intervalRef.current = window.setInterval(() => {
      console.log('tick');
    }, 1000);
  };

  const stop = () => {
    if (intervalRef.current !== null) {
      clearInterval(intervalRef.current);
    }
  };
  // ...
}
```

---

## useState との違い

| | useState | useRef |
| --- | --- | --- |
| 値の保持 | ✅ | ✅ |
| 値を変えると再レンダリング | **する** | **しない** |
| 値へのアクセス | `state` | `ref.current` |
| 主な用途 | 画面に反映する状態 | DOM 参照・再描画不要な値 |

> 「画面に表示する値」なら `useState`、「裏で持っておきたいだけの値」なら `useRef`、と覚えると分かりやすい。

---

## よくあるミス

- **レンダリング中に `ref.current` を読み書きする**：レンダリング中の参照は予測しづらいので避ける。読み書きはイベントハンドラや `useEffect` の中で行うのが基本
- **画面に表示したい値を ref で管理する**：`ref.current` を変えても再描画されないため、表示は更新されない。表示する値は `useState` を使う
- **初期レンダリング時に DOM ref を参照する**：マウント前は `ref.current` が `null`。`useEffect` 内や、null チェック（`?.`）を入れて使う

---

## 参考

- [React 公式ドキュメント - useRef](https://react.dev/reference/react/useRef)
- [Referencing Values with Refs](https://react.dev/learn/referencing-values-with-refs)
- [Manipulating the DOM with Refs](https://react.dev/learn/manipulating-the-dom-with-refs)
