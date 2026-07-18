# SWR

## 概要

SWR は、React 向けの **データ取得（サーバー状態の管理）ライブラリ**。
Next.js を作っている **Vercel** が開発している。

名前の「SWR」は **stale-while-revalidate**（HTTP キャッシュの戦略）の略。
> **まずキャッシュ（古い＝stale）を表示 → 裏で最新を取得（revalidate）→ 差し替える**

という動きが名前の由来になっている。これにより「表示は速く、データは新しく」を両立する。

---

## なぜ必要か？

データ取得を [useEffect](hooks/useEffect.md) + [useState](hooks/useState.md) で自前実装すると、
キャッシュ・再取得・ローディング／エラー管理を毎回書くことになり大変。

SWR はこれらを**シンプルな API で肩代わり**してくれる。
考え方は [TanStack Query](tanstack-query.md) と同じ「サーバー状態の専用ツール」。

---

## 基本の使い方

### インストール

```bash
npm install swr
```

### データ取得（useSWR）

```tsx
import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then((res) => res.json());

function Users() {
  const { data, error, isLoading } = useSWR('/api/users', fetcher);

  if (isLoading) return <p>読み込み中...</p>;
  if (error) return <p>エラーが発生しました</p>;

  return <ul>{data.map((u) => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

`useSWR` に渡すのは 2 つ。

| 引数 | 役割 |
| --- | --- |
| **key** | キャッシュの識別子（ここでは URL） |
| **fetcher** | 実際にデータを取得する関数 |

同じ key を使えばキャッシュが共有されるので、複数コンポーネントで使っても**通信は 1 回**で済む。

---

## SWR の特徴的な挙動

デフォルトで、次のようなタイミングで**自動的に再取得（revalidate）**してくれる。

- コンポーネントのマウント時
- ウィンドウにフォーカスが戻ったとき（別タブから戻ってきた等）
- ネットワークが再接続したとき

これにより、ユーザーが操作しなくても表示が自然に最新へ近づく。
（不要なら `revalidateOnFocus: false` などで無効化できる）

---

## データの更新（mutate）

データを変更したあとにキャッシュを更新したいときは `mutate` を使う。

```tsx
import useSWR, { mutate } from 'swr';

// 追加処理のあと、一覧を再取得させる
await fetch('/api/users', { method: 'POST', body: ... });
mutate('/api/users'); // このkeyのデータを再検証（再取得）
```

「更新したら一覧も最新にする」という流れを `mutate` で表現する。

---

## よく使うオプション

| オプション | 役割 |
| --- | --- |
| `revalidateOnFocus` | フォーカス時に再取得するか（デフォルト `true`） |
| `refreshInterval` | 一定間隔でポーリング再取得する（ミリ秒） |
| `dedupingInterval` | 同じ key の重複リクエストをまとめる時間 |
| `fallbackData` | 取得前に表示しておく初期データ |

---

## TanStack Query との違い

どちらも「サーバー状態の管理」だが、性格が少し違う。

| | SWR | [TanStack Query](tanstack-query.md) |
| --- | --- | --- |
| 開発元 | Vercel | TanStack |
| 特徴 | **軽量・シンプル**。最小構成で使いやすい | 高機能・多機能。細かい制御がしやすい |
| 対応 | React 中心 | React / Vue / Svelte など |
| 向いている場面 | まず手軽に導入したいとき | 複雑なキャッシュ制御が必要なとき |

> ざっくり「**シンプルに始めたいなら SWR、機能重視なら TanStack Query**」という住み分け。どちらも stale-while-revalidate の考え方がベース。

---

## まとめ

- SWR は **Vercel 製の、軽量でシンプルなデータ取得ライブラリ**
- 名前どおり **stale-while-revalidate**（まずキャッシュ→裏で最新取得）が核
- `useSWR` で取得、`mutate` で更新。フォーカス復帰などで自動再取得してくれる
- 役割は [TanStack Query](tanstack-query.md) と同じで、より手軽な選択肢

サーバー状態を扱うライブラリはいくつかあるが、**同じ「stale-while-revalidate」という思想**を共有しているのが分かって理解が繋がった。

---

## 参考

- [SWR 公式ドキュメント](https://swr.vercel.app/)
