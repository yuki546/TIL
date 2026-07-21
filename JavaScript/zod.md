# TIL: zod とは何か

## 概要

zod（ゾッド）は、TypeScript 向けの **スキーマ定義＆バリデーション（値の検証）ライブラリ**。

「このデータはこういう形（型・条件）であるべき」という **スキーマ** を定義しておき、
実際の値がそれに合っているかを実行時にチェックできる。

TypeScript の型は **コンパイル時** の仕組みなので、実行時（API のレスポンスやフォーム入力など）に
「本当にその型どおりの値が来ているか」は保証してくれない。zod はそこを **実行時に検証**して埋めてくれる。

---

## なぜ必要か？（型は実行時に消える）

TypeScript の型は、ビルド後の JavaScript では**消えてしまう**。
そのため、外から来るデータ（API・フォーム・localStorage など）は型注釈があっても信用できない。

```ts
// 型上は User だが、実際に何が来るかは保証されない
const user: User = await fetch('/api/user').then((r) => r.json());
```

zod を使うと、実行時に「本当にその形か」を検証できる。

```ts
import { z } from 'zod';

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
});

const data = await fetch('/api/user').then((r) => r.json());
const user = UserSchema.parse(data); // 形が違えばここでエラーになる
```

---

## 基本の使い方

### スキーマを定義する

```ts
import { z } from 'zod';

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),      // メール形式
  age: z.number().min(0),         // 0以上
  role: z.enum(['admin', 'user']), // どちらか
  bio: z.string().optional(),     // 任意（あってもなくてもよい）
});
```

### 検証する（parse / safeParse）

```ts
// parse：失敗したら例外を投げる
const user = UserSchema.parse(data);

// safeParse：例外を投げず、結果をオブジェクトで返す
const result = UserSchema.safeParse(data);
if (result.success) {
  console.log(result.data);   // 検証済みのデータ
} else {
  console.log(result.error);  // エラーの詳細
}
```

| メソッド | 失敗時の挙動 | 向いている場面 |
| --- | --- | --- |
| `parse` | 例外を throw する | 失敗＝異常として扱いたいとき |
| `safeParse` | `{ success, data / error }` を返す | フォーム検証など、失敗も普通に扱いたいとき |

---

## スキーマから型を作れる（zod の強み）

zod の一番うれしい点は、**スキーマから TypeScript の型を自動生成できる**こと。
`z.infer` を使えば、型定義とバリデーションを**二重に書かなくて済む**。

```ts
const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
});

// スキーマから型を生成
type User = z.infer<typeof UserSchema>;
// → { id: number; name: string }
```

> 「型 → 別途バリデーションも書く」ではなく、「スキーマ 1 つから型もバリデーションも得られる」のが zod の核心。

---

## よく使う定義

| 記法 | 意味 |
| --- | --- |
| `z.string()` / `z.number()` / `z.boolean()` | 基本の型 |
| `z.array(z.string())` | 文字列の配列 |
| `z.object({...})` | オブジェクト |
| `z.enum(['a', 'b'])` | 決まった値のどれか |
| `.optional()` | 任意（省略可能） |
| `.nullable()` | `null` を許可 |
| `.min()` / `.max()` / `.email()` / `.url()` | 追加の制約 |
| `.default(値)` | 未指定時のデフォルト値 |

---

## よく使われる場面

- **API レスポンスの検証**：外部から来るデータを信頼せず、スキーマで検証してから使う
- **フォームのバリデーション**：React Hook Form などと組み合わせて入力チェック
- **環境変数の検証**：`.env` の値が揃っているかを起動時にチェック
- **[Vitest](vitest.md) など**：テスト対象の入出力の形をスキーマで担保する

---

## まとめ

- zod は **実行時に値の形を検証**できる TypeScript 向けライブラリ
- TypeScript の型が届かない「外から来るデータ」の安全性を高められる
- **スキーマ 1 つから型（`z.infer`）とバリデーションの両方**が得られるのが最大の強み
- `parse`（例外）と `safeParse`（結果オブジェクト）を場面で使い分ける

「型は実行時に消える」という当たり前だけど見落としがちな点を、zod が埋めてくれるのだと理解できた。

---

## 参考

- [Zod 公式ドキュメント](https://zod.dev/)
