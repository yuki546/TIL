# useMemo

## 概要

`useMemo` は React のフックで、**計算結果を「メモ化（記憶）」して、無駄な再計算を防ぐ** ためのもの。
依存する値が変わらない限り、前回の計算結果を使い回してくれる。

主な用途：
- 重い計算処理の結果をキャッシュする
- オブジェクト・配列の**参照を安定させる**（再レンダリングの抑制）

---

## なぜ必要か？

React はコンポーネントが再レンダリングされるたびに、**中の処理をすべて実行し直す**。
そのため、重い計算があると再レンダリングのたびに毎回計算されてしまう。

```tsx
function List({ items }) {
  // 再レンダリングのたびに毎回ソートが走る（items が変わっていなくても）
  const sorted = heavySort(items);
  // ...
}
```

`useMemo` を使うと、**依存配列が変わったときだけ**計算するようになる。

---

## 基本構文

```tsx
const memoized = useMemo(() => 計算処理, [依存配列]);
```

- 第 1 引数：値を返す関数
- 第 2 引数：依存配列。ここの値が変わったときだけ再計算される

```tsx
function List({ items }) {
  const sorted = useMemo(() => heavySort(items), [items]);
  // items が変わらなければ、前回のソート結果を再利用
}
```

---

## 用途2：参照の安定化

オブジェクトや配列は、再レンダリングのたびに**新しい参照**として作られる。
これを `useMemo` で安定させると、`useEffect` の依存配列や `React.memo` と組み合わせたときに無駄な再実行・再描画を防げる。

```tsx
// 毎レンダリングで options が「別物」になり、useEffect が毎回実行される
const options = { limit: 10 };

useEffect(() => {
  fetchData(options);
}, [options]); // ← options の参照が毎回変わる

// useMemo で参照を固定
const options = useMemo(() => ({ limit: 10 }), []);
```

---

## useMemo と useCallback の違い

似たフックに `useCallback` がある。

| フック | メモ化するもの |
| --- | --- |
| `useMemo` | 計算結果（**値**） |
| `useCallback` | 関数そのもの |

> `useCallback(fn, deps)` は `useMemo(() => fn, deps)` と同じ意味。
> 「値をキャッシュしたい → useMemo」「関数をキャッシュしたい → useCallback」と覚える。

---

## よくあるミス・注意点

- **何でもかんでも useMemo する**：メモ化自体にもコスト（依存配列の比較・メモリ）がある。軽い計算にまで使うと逆効果。**重い計算や参照の安定化が必要なときだけ**使う
- **依存配列の指定漏れ**：計算内で使っている値を依存配列に入れ忘れると、古い結果を使い続ける（[useEffect](useEffect.md) と同じ注意点）
- **副作用を書いてしまう**：`useMemo` は「値を計算して返す」場所。データフェッチや DOM 操作などの副作用は [useEffect](useEffect.md) に書く

---

## まとめ

- `useMemo` は **計算結果をメモ化して無駄な再計算を防ぐ**フック
- 用途は「重い計算のキャッシュ」と「参照の安定化」の 2 つ
- ただし**乱用は禁物**。効果がある場所だけに使うのが大事

パフォーマンスの最適化は「まず計測してから」が基本で、`useMemo` も必要になってから入れるくらいでちょうど良いと感じた。

---

## 参考

- [React 公式ドキュメント - useMemo](https://react.dev/reference/react/useMemo)
- [useCallback](https://react.dev/reference/react/useCallback)
