# TanStack Query

## 概要

TanStack Query は、**サーバーから取得したデータ（サーバー状態）を管理する**ためのライブラリ。
データ取得・キャッシュ・再取得・エラー処理などを、まとめて面倒みてくれる。

以前は **React Query** という名前だったが、React 以外（Vue / Svelte / Solid など）にも対応したことで
**TanStack Query** に改名された。React で使う場合のパッケージ名は `@tanstack/react-query`。

---

## なぜ必要か？（useEffect + useState の限界）

データ取得は [useEffect](hooks/useEffect.md) + [useState](hooks/useState.md) でも書けるが、
自前でやろうとすると考えることが一気に増える。

```tsx
// よくある自前実装
const [data, setData] = useState(null);
const [isLoading, setIsLoading] = useState(true);
const [error, setError] = useState(null);

useEffect(() => {
  fetch('/api/users')
    .then((res) => res.json())
    .then(setData)
    .catch(setError)
    .finally(() => setIsLoading(false));
}, []);
```

これだけでも動くが、実際には次のような要件が次々出てくる。

- 同じデータを複数のコンポーネントで使いたい（毎回取得したくない）
- 一度取得したデータをキャッシュしたい
- 画面に戻ってきたら最新データに更新したい
- 失敗したらリトライしたい
- ローディング／エラーの状態管理が毎回コピペになる

TanStack Query は、**これらを最初から備えている**のが最大の価値。

---

## 「クライアント状態」と「サーバー状態」

TanStack Query を理解するうえで大事な考え方。

| | クライアント状態 | サーバー状態 |
| --- | --- | --- |
| 例 | モーダルの開閉、フォーム入力 | API から取得したユーザー一覧 |
| 持ち主 | 自分（アプリ側） | サーバー側 |
| 特徴 | 自分で完全にコントロールできる | 非同期・いつの間にか古くなる |
| 向いている道具 | [useState](hooks/useState.md) / [useContext](hooks/useContext.md) | **TanStack Query** |

> サーバー状態は「借りているデータ」なので、**キャッシュと鮮度管理**が本質的に必要になる。ここが専用ライブラリの出番。

---

## 基本の使い方

### インストールとセットアップ

```bash
npm install @tanstack/react-query
```

アプリ全体を `QueryClientProvider` で囲む。

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Users />
    </QueryClientProvider>
  );
}
```

### データ取得（useQuery）

```tsx
import { useQuery } from '@tanstack/react-query';

function Users() {
  const { data, isPending, error } = useQuery({
    queryKey: ['users'],                                  // キャッシュの識別子
    queryFn: () => fetch('/api/users').then((r) => r.json()), // 取得処理
  });

  if (isPending) return <p>読み込み中...</p>;
  if (error) return <p>エラーが発生しました</p>;

  return <ul>{data.map((u) => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

先ほどの自前実装が、これだけになる。

---

## queryKey がカギ

`queryKey` は **キャッシュの住所**のようなもの。同じキーなら同じキャッシュが使われる。

```tsx
queryKey: ['users']          // ユーザー一覧
queryKey: ['users', userId]  // 特定ユーザー（idごとに別キャッシュ）
```

- 別のコンポーネントで同じ `queryKey` を使えば、**キャッシュが再利用される**（無駄な通信をしない）
- キーの一部（`userId` など）が変われば、自動的に新しいデータを取りに行く

---

## データの更新（useMutation）

作成・更新・削除など、**データを変更する操作**には `useMutation` を使う。

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function AddUser() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newUser) =>
      fetch('/api/users', { method: 'POST', body: JSON.stringify(newUser) }),
    onSuccess: () => {
      // 一覧のキャッシュを「古い」と印をつけて再取得させる
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });

  return <button onClick={() => mutation.mutate({ name: 'taro' })}>追加</button>;
}
```

> 「更新したら一覧も最新にしたい」を `invalidateQueries` で表現するのが定番パターン。

---

## 主なオプション

| オプション | 役割 |
| --- | --- |
| `staleTime` | データを「新鮮」とみなす時間。この間は再取得しない（デフォルト `0`） |
| `gcTime` | 使われなくなったキャッシュを保持する時間（旧 `cacheTime`） |
| `enabled` | `false` の間はクエリを実行しない（条件付き取得に使う） |
| `retry` | 失敗時のリトライ回数 |

`staleTime` が `0` なので、デフォルトでは画面復帰時などに積極的に再取得される。
あまり変わらないデータなら `staleTime` を長めにすると通信を減らせる。

---

## 注意点

- **クライアント状態まで入れない**：モーダルの開閉などは [useState](hooks/useState.md) の役割。サーバー状態専用と考える
- **queryKey の付け方を統一する**：バラバラだとキャッシュが共有されず、意図しない再取得が起きる
- **v4 → v5 で変更あり**：`cacheTime` → `gcTime`、`isLoading` → `isPending` など。記事を読むときはバージョンに注意

---

## まとめ

- TanStack Query は **サーバー状態（API データ）の管理に特化**したライブラリ
- `useQuery` で取得、`useMutation` で更新、`queryKey` でキャッシュを識別する
- キャッシュ・再取得・ローディング／エラー管理を任せられるので、**自前の useEffect 実装が大幅に減る**

「状態管理」とひとくくりにせず、**クライアント状態とサーバー状態を分けて考える**という視点が一番の学びだった。

---

## 参考

- [TanStack Query 公式ドキュメント](https://tanstack.com/query/latest)
