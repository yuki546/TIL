# TIL: Vitest とは何か

## 概要

Vitest（ヴィテスト）は、[Vite](vite.md) をベースにしたフロントエンド向けの **テストフレームワーク**。
「Vite + test」で Vitest という名前のとおり、Vite の設定や仕組みをそのまま活かしてテストを実行できるのが特徴。

これまで JavaScript のテストでは **Jest** がよく使われてきたが、
Vite プロジェクトでは設定を二重に持つ必要があったり、ESM との相性で手間がかかることがあった。
Vitest は Vite の設定（`vite.config.ts`）を共有できるため、その手間を減らせる。

---

## メリット

- **Vite の設定を再利用できる**：`vite.config.ts` のエイリアスやプラグインをテストでもそのまま使える
- **起動・実行が速い**：Vite の仕組み（ESM ネイティブ）を活かして高速
- **Jest 互換の API**：`describe` / `it` / `expect` などがほぼ同じ書き方。Jest からの移行がしやすい
- **ウォッチモードが快適**：変更したファイルに関係するテストだけを再実行してくれる（HMR 的な体験）
- **TypeScript / JSX を追加設定なしで扱える**

---

## デメリット・注意点

- **比較的新しい**：Jest に比べると歴史が浅く、情報量や事例はまだ少なめ（ただし急速に増加中）
- **Vite が前提**：Vite を使っていないプロジェクトでは、メリットが薄くなる
- **Jest とは細部が違う**：API はほぼ互換だが、設定や一部の挙動は異なるので移行時は確認が必要

---

## 基本の使い方

### インストール

```bash
npm install -D vitest
```

`package.json` にスクリプトを追加：

```json
{
  "scripts": {
    "test": "vitest"
  }
}
```

### テストコードの例

```ts
import { describe, it, expect } from 'vitest';

function add(a: number, b: number) {
  return a + b;
}

describe('add', () => {
  it('1 + 2 は 3 になる', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

### 実行

```bash
npm test          # ウォッチモードで起動（デフォルト）
npx vitest run    # 1回だけ実行（CI 向け）
```

---

## よく使う API

| API | 役割 |
| --- | --- |
| `describe` | テストをグループにまとめる |
| `it` / `test` | 個々のテストケースを定義する |
| `expect` | 実際の値と期待値を比較する（アサーション） |
| `beforeEach` / `afterEach` | 各テストの前後で共通処理を実行する |
| `vi.fn()` / `vi.mock()` | モック（関数や依存の差し替え）を作る |

> Jest では `jest.fn()` だが、Vitest では **`vi.fn()`** を使う点が違う。

---

## React コンポーネントのテスト

`@testing-library/react` と組み合わせると、React コンポーネントのテストもできる。
`jsdom`（ブラウザ環境を模した仮想 DOM）を使う設定を加えるのが一般的。

```ts
// vite.config.ts
export default defineConfig({
  test: {
    environment: 'jsdom',
  },
});
```

---

## まとめ

- Vitest は **Vite ベースの高速なテストフレームワーク**
- **Vite の設定を共有でき、Jest 互換の API** なので導入・移行がしやすい
- Vite プロジェクトでテストを書くなら第一候補になりやすい

[Vite](vite.md) でプロジェクトを作ったら、テストは Vitest でそのまま揃えると設定がシンプルになると感じた。

---

## 参考

- [Vitest 公式ドキュメント](https://vitest.dev/)
