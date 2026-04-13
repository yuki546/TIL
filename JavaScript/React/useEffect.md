# useEffect

## 概要

`useEffect` は React のフックで、**副作用（side effects）** を関数コンポーネントで扱うためのもの。
「レンダリングの外側で起きる処理」を書く場所と捉えるとわかりやすい。

副作用の例：
- データフェッチ
- DOM の直接操作
- タイマー（`setTimeout` / `setInterval`）
- イベントリスナーの登録・解除

---

## 基本構文

```tsx
useEffect(() => {
  // 副作用の処理
}, [依存配列]);
```

---

## 依存配列による挙動の違い

| 依存配列 | 実行タイミング |
|---|---|
| なし | 毎レンダリング後 |
| `[]` | マウント時のみ |
| `[value]` | `value` が変化したレンダリング後 |

---

## クリーンアップ

`return` でクリーンアップ関数を返すと、**アンマウント時** または **次の effect 実行前** に呼ばれる。

```tsx
useEffect(() => {
  const id = setInterval(() => {
    console.log('tick');
  }, 1000);

  return () => clearInterval(id); // クリーンアップ
}, []);
```

イベントリスナーの登録・解除でも同様のパターンを使う。

---

## よくあるミス

- **依存配列の指定漏れ**：effect 内で参照している state や props を依存配列に入れ忘れると、古い値を参照し続ける（stale closure）
- **オブジェクト・配列を依存配列に入れる**：毎レンダリングで参照が変わるため無限ループになりやすい → `useMemo` / `useCallback` で安定させる
- **非同期関数を直接渡す**：`useEffect(async () => {...})` は NG。内部で即時実行関数として定義する

```tsx
// NG
useEffect(async () => {
  const data = await fetch('/api');
}, []);

// OK
useEffect(() => {
  const fetchData = async () => {
    const data = await fetch('/api');
  };
  fetchData();
}, []);
```

---

## React 18 の Strict Mode での挙動

開発環境では effect が **2回実行** される（マウント → アンマウント → 再マウント）。
クリーンアップを正しく実装できているかを確認するための仕様。本番環境では1回のみ。

---

## 参考

- [React 公式ドキュメント - useEffect](https://react.dev/reference/react/useEffect)
- [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
