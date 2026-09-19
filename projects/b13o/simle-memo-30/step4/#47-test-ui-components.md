# Testing Library の `screen`

`@testing-library/react` が export しているオブジェクト。
**「画面（=`document.body`）から要素を探すためのクエリがまとまったもの」** と考えるとよい。

```ts
import { render, screen } from '@testing-library/react'

render(<Editor onSubmit={onSubmit} />)
const textarea = screen.getByRole('textbox', { name: 'メモを入力...' })
```

## なぜ `render()` の戻り値ではなく `screen` なのか

`render()` の戻り値からも同じクエリを取り出せる。

```ts
const { getByRole } = render(<Editor onSubmit={onSubmit} />) // これでも動く
```

ただし、使いたいクエリが増えるたびに分割代入に足していく必要がある。
`screen` は常に `document.body` 全体を見ているので、import 1つで済む。公式も `screen` を推す。

- `render()` の戻り値 … その render が返した範囲
- `screen` … `document.body` 全体（ポータルやモーダルも拾える）

## `getByRole` の `name` はアクセシブルネーム

```ts
screen.getByRole("textbox", { name: "メモを入力..." });
screen.getByRole("button", { name: "送信" });
```

`name` は「変数名」でも「HTML の name 属性」でもなく、**支援技術がその要素をどう読み上げるか**（アクセシブルネーム）。
`Editor.tsx` 側では `aria-label` で付けている。

```tsx
<textarea placeholder={placeholder} aria-label={placeholder} />
<button aria-label={isEditing ? '更新' : '送信'}><Send size={18} /></button>
```

ボタンの中身はアイコン（SVG）だけでテキストがないため、`aria-label` がないと `name` で探せない。
**テストのために `aria-label` を足したのではなく、スクリーンリーダー利用者にとって必要なものが、そのままテストの手掛かりになっている**という順番。

## `getBy` / `queryBy` / `findBy`

| 接頭辞    | 見つからないとき        | 使いどころ                   |
| --------- | ----------------------- | ---------------------------- |
| `getBy`   | その場で例外            | あるはずの要素               |
| `queryBy` | `null` を返す           | **ない**ことを確かめたいとき |
| `findBy`  | Promise（一定時間待つ） | 非同期で後から出てくる要素   |

`getBy` は複数マッチしても例外になる。

## `screen` が見ているのは「前のテストの残骸」も含む

`screen` は `document.body` を見るだけなので、テスト間で DOM を片付けないと前のテストが描画した要素が残り、`getBy` が「複数見つかった」で落ちる。

このプロジェクトでは `src/test/setup.ts` で明示的に片付けている。

```ts
afterEach(() => {
  cleanup();
});
```

（`globals: true` なら自動で走るが、この設定では自前で呼ぶ必要がある）

## 詰まったら `screen.debug()`

今の DOM をコンソールに出してくれる。`getByRole` が見つからないときはまずこれ。

```ts
screen.debau(); // body 全体
screen.debug(screen.getByRole("button")); // 一部だけ
```

`screen.getByRole('button')` のようにオプションなしで呼ぶと、複数マッチ時のエラーメッセージに**候補の role と name の一覧**が出るので、それを見て `name` を決めるのが早い。
