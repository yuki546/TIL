# useState

## 概要

`useState` は React のフックで、**関数コンポーネントに「状態（state）」を持たせる** ためのもの。
状態とは「変化する値」のことで、これが変わると React がコンポーネントを**再レンダリング**してくれる。

状態の例：
- フォームの入力値
- カウンターの数値
- モーダルの開閉フラグ（true / false）
- API から取得したデータ

---

## 基本構文

```tsx
const [state, setState] = useState(初期値);
```

- `state`：現在の状態の値
- `setState`：状態を更新するための関数
- `初期値`：最初のレンダリング時にだけ使われる値

```tsx
const [count, setCount] = useState(0);

return (
  <button onClick={() => setCount(count + 1)}>
    クリック数: {count}
  </button>
);
```

`setCount` を呼ぶと state が更新され、コンポーネントが再レンダリングされる。

---

## 状態の更新は「直接書き換え」ではなく setState で

state を直接書き換えても React は再レンダリングしてくれない。**必ず更新関数を使う**。

```tsx
// NG：再レンダリングされない
count = count + 1;

// OK
setCount(count + 1);
```

---

## 関数型更新（前の状態をもとに更新する）

連続して更新したり、前の値をもとに計算する場合は、**関数を渡す形**が安全。

```tsx
// NG：同じレンダリング内では count が古いままで +1 しか増えない
setCount(count + 1);
setCount(count + 1);

// OK：prev に最新の値が渡るので +2 増える
setCount((prev) => prev + 1);
setCount((prev) => prev + 1);
```

> state の更新は**非同期的にまとめて処理（バッチ処理）**されるため、最新の値を使いたいときは関数型更新が安全。

---

## オブジェクト・配列の更新は「新しい値」を作る

React は state の**参照（メモリ上の同一性）**で変化を判断する。
そのため、オブジェクトや配列は**直接書き換えず、新しいものを作って渡す**。

```tsx
const [user, setUser] = useState({ name: 'taro', age: 20 });

// NG：同じオブジェクトを書き換えても再レンダリングされない
user.age = 21;
setUser(user);

// OK：スプレッド構文で新しいオブジェクトを作る
setUser({ ...user, age: 21 });
```

配列も同様に、`push` などの破壊的操作ではなく新しい配列を作る。

```tsx
// OK：新しい配列を作って追加
setItems((prev) => [...prev, newItem]);
```

---

## 初期値が重い場合は「遅延初期化」

初期値の計算が重い処理のときは、**関数を渡す**と初回レンダリング時にだけ実行される。

```tsx
// 毎レンダリングで実行されてしまう
const [value, setValue] = useState(heavyCalc());

// 初回だけ実行される（遅延初期化）
const [value, setValue] = useState(() => heavyCalc());
```

---

## よくあるミス

- **state を直接書き換える**：`state = ...` や `array.push()` では再レンダリングされない
- **更新直後に新しい値を参照しようとする**：`setCount(count + 1)` の直後の `count` はまだ古い値（更新は次のレンダリングで反映される）
- **複数の状態を 1 つの大きなオブジェクトに詰め込みすぎる**：更新が複雑になりがち。関連しないものは分ける

---

## 参考

- [React 公式ドキュメント - useState](https://react.dev/reference/react/useState)
- [State: A Component's Memory](https://react.dev/learn/state-a-components-memory)
