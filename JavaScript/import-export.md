# import / export（default と named の違い）

## 概要

import するときに `{}` が付くときと付かないときがあるのは、**エクスポートする側がどの方法で export しているか**が違うから。

- `{}` なし → **default export**（デフォルトエクスポート）
- `{}` あり → **named export**（名前付きエクスポート）

これは React ではなく、JavaScript（ES Modules）の仕組み。

```jsx
import React, { useState } from "react";
//     ^^^^^  ^^^^^^^^^^^^
//     default   named
```

---

## 用語の整理

```jsx
import { useState } from "react";
//       ^^^^^^^^        ^^^^^^^
//       export された値  モジュール
```

- `from` の後ろ → **モジュール**
- `{}` の中や `import` の直後 → モジュールが export している**値**（関数・変数・コンポーネントなど）

| 用語           | 意味                                                         | 例                           |
| -------------- | ------------------------------------------------------------ | ---------------------------- |
| **モジュール** | import / export の単位。基本は **1 ファイル = 1 モジュール** | `./Button.jsx`、`./utils.js` |
| **パッケージ** | npm で配布される単位。中に複数のモジュールが入っている       | `react`、`zod`               |
| **ライブラリ** | 再利用できるようにまとめたコード（役割を表す一般的な言葉）   | React、zod                   |

- `import Button from "./Button"` → **自作のモジュール**から import（ライブラリではない）
- `import { useState } from "react"` → **React というライブラリ**のモジュールから import

→ 「ライブラリもモジュールの形で提供されている」
import 元を指す言葉としては「モジュール」はいつでも正しく、「ライブラリ」は外部パッケージの時だけ使える。

---

## default export（`{}` なし）

1 ファイルにつき **1 つだけ**持てる「メインの値」

```jsx
// Button.jsx
export default function Button() {
  return <button>Click</button>;
}
```

```jsx
import Button from "./Button";
import MyButton from "./Button"; // 名前は自由に付けられる
```

- import する側で**好きな名前**を付けられる
- 1 ファイルに 1 つまで

---

## named export（`{}` あり）

**名前を付けて複数** export できる。

```jsx
// utils.js
export const add = (a, b) => a + b;
export const sub = (a, b) => a - b;
```

```js
import { add, sub } from "./utils";
import { add as sum } from "./utils"; // 別名にしたいときは as
```

- export したときと**同じ名前**で受け取る必要がある
- `{}` は「この名前のものを取り出す」という意味（分割代入に似ている）
- 1 ファイルにいくつでも書ける

---

## 比較表

|        | default export     | named export            |
| ------ | ------------------ | ----------------------- |
| export | `export default X` | `export const X`        |
| import | `import X from`    | `import { X } from`     |
| 個数   | 1 ファイル 1 つ    | いくつでも              |
| 名前   | 自由に変えられる   | 同じ名前（`as` で別名） |

---

## よくあるミス

default export されているものを `{}` 付きで import すると `undefined` になる（逆も同じ）。

```jsx
// Button.jsx は export default
import { Button } from "./Button"; // NG：undefined になる
import Button from "./Button"; // OK
```

React ではこのとき次のようなエラーが出る。

```
Element type is invalid: expected a string ... but got: undefined.
```

→ import がうまくいかないときは、**まず export している側を確認する**。

---

## まとめ

`{}` 付きで import するのは モジュール内で `default` 無しで `export` されているとき、`{}` 無しで import するのは モジュール内で `default` 付きで `export` されているとき。

`default` が付いた `export` は1つのモジュール内に1つのみ。
