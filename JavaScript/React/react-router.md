# React Router

## 概要

`React Router` は、React で **ルーティング（URL に応じた画面の切り替え）** を実現するためのライブラリ。

React は本来ページ遷移の仕組みを持たない **SPA（Single Page Application）** 向けのライブラリなので、
「`/about` を開いたら About ページを表示する」といった URL と画面の対応づけを React Router が担う。

主な役割：
- URL に応じて表示するコンポーネントを切り替える
- ページ遷移（リンク・プログラムからの遷移）
- URL パラメータやクエリの取得
- ネストしたルーティング

---

## SPA のルーティングとは？

通常の Web サイトは、ページ遷移のたびにサーバーへ新しい HTML を取りに行く。
一方 SPA では、**最初に読み込んだ 1 ページの中で JavaScript が表示を差し替える**。

React Router は、この「URL は変わるがページ再読み込みはしない」動きを実現してくれる。

> ブラウザの History API を使って、リロードなしで URL と表示を同期させている。

---

## 基本の使い方

インストールして、`react-router-dom` から必要なものを import する。

```bash
npm install react-router-dom
```

### ルートの定義

```tsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users/:id" element={<UserDetail />} />
      </Routes>
    </BrowserRouter>
  );
}
```

| 要素 | 役割 |
| --- | --- |
| `BrowserRouter` | ルーティング全体を囲むラッパー |
| `Routes` | 複数の `Route` をまとめる |
| `Route` | `path`（URL）と `element`（表示するコンポーネント）の対応 |

---

## ページ遷移（リンク）

`<a>` タグではなく `<Link>` を使う。`<a>` だとページ全体が再読み込みされてしまう。

```tsx
import { Link } from 'react-router-dom';

<Link to="/about">Aboutへ</Link>
```

現在のページかどうかでスタイルを変えたい場合は `<NavLink>` を使う。

---

## プログラムからの遷移

ボタンのクリックや処理完了後に遷移したいときは `useNavigate` を使う。

```tsx
import { useNavigate } from 'react-router-dom';

function LoginButton() {
  const navigate = useNavigate();

  const handleLogin = () => {
    // ログイン処理...
    navigate('/dashboard'); // 遷移
  };

  return <button onClick={handleLogin}>ログイン</button>;
}
```

---

## URL パラメータの取得

`/users/:id` のような動的な部分は `useParams` で取り出せる。

```tsx
import { useParams } from 'react-router-dom';

function UserDetail() {
  const { id } = useParams(); // /users/1 なら id = "1"
  return <p>ユーザーID: {id}</p>;
}
```

---

## よく使うフック・機能

| 機能 | 説明 |
| --- | --- |
| `useNavigate` | プログラムからページ遷移する |
| `useParams` | URL パラメータ（`:id` など）を取得する |
| `useLocation` | 現在の URL 情報（パス・クエリなど）を取得する |
| `useSearchParams` | クエリ文字列（`?key=value`）を読み書きする |
| `<Outlet />` | ネストしたルートの子要素を表示する場所 |

---

## ネストしたルーティング

共通レイアウト（ヘッダーなど）の中で子ページを切り替えたいときは、`Route` を入れ子にして `<Outlet />` を使う。

```tsx
<Route path="/dashboard" element={<Layout />}>
  <Route index element={<Home />} />       {/* /dashboard */}
  <Route path="settings" element={<Settings />} /> {/* /dashboard/settings */}
</Route>
```

`Layout` の中に `<Outlet />` を置くと、そこに子ルートの内容が表示される。

---

## 注意点

- **`<a>` タグを使うと再読み込みが走る**：SPA の利点が失われるので、内部リンクは `<Link>` / `<NavLink>` を使う
- **バージョンによる書き方の違い**：v5 と v6 で API が大きく変わっている（`Switch` → `Routes`、`component` → `element` など）。記事やサンプルを見るときはバージョンに注意
- **Next.js などのフレームワークでは不要な場合も**：[Next.js](Next.js/Gista.js/about.md) は独自のルーティングを持つため、React Router は使わない

---

## 参考

- [React Router 公式ドキュメント](https://reactrouter.com/)
