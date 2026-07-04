# React Hooks

React のフック（Hooks）について学んだことをまとめたメモ集📝

## 一覧

| Hook | 役割 |
| --- | --- |
| [useState](useState.md) | コンポーネントに状態（state）を持たせる |
| [useEffect](useEffect.md) | 副作用（データ取得・タイマー・購読など）を扱う |
| [useRef](useRef.md) | 再描画不要な値の保持・DOM 要素への参照 |
| [useContext](useContext.md) | コンポーネントツリー全体でデータを共有する |
| [useMemo](useMemo.md) | 計算結果をメモ化して無駄な再計算を防ぐ |

## 学習メモ

- 基本の状態管理は [useState](useState.md)、副作用は [useEffect](useEffect.md) が土台
- パフォーマンス最適化（[useMemo](useMemo.md) など）は「まず計測してから」必要な箇所だけに使う
- 次に書きたい：`useCallback`（useMemo と対になる）、`useReducer`、カスタムフック
