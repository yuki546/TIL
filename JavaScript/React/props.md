# Props

## 概要

Props（プロップス／properties の略）は、**親コンポーネントから子コンポーネントへデータを渡す仕組み**。

React の UI はコンポーネントを組み合わせて作るが、その部品同士をつなぐのが Props。
「関数に引数を渡す」感覚に近く、同じコンポーネントを**渡す値だけ変えて使い回す**ことができる。

```tsx
// 親：値を渡す
<Greeting name="taro" />

// 子：受け取って使う
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}
```

---

## 基本の使い方

### 渡す側（親）

HTML の属性のように `属性名={値}` で渡す。

```tsx
<UserCard name="taro" age={20} isAdmin={true} />
```

- 文字列は `"..."`、それ以外（数値・真偽値・変数など）は `{}` で囲む

### 受け取る側（子）

第 1 引数の `props` オブジェクトで受け取る。**分割代入**で書くのが一般的。

```tsx
// props オブジェクトで受け取る
function UserCard(props) {
  return <p>{props.name}（{props.age}歳）</p>;
}

// 分割代入で書くとスッキリ
function UserCard({ name, age }) {
  return <p>{name}（{age}歳）</p>;
}
```

---

## Props は「読み取り専用」

Props の最大のルールは、**子コンポーネント側で書き換えてはいけない**こと。

```tsx
function UserCard({ name }) {
  name = 'changed'; // NG：Props を書き換えてはいけない
  return <p>{name}</p>;
}
```

React では **データは親から子へ一方向に流れる**（単方向データフロー）。
子が勝手に変えられると、どこで値が変わったか追えなくなるため、Props は「上から渡された、変更できないもの」として扱う。

> 値を変化させたい場合は、[useState](hooks/useState.md) の状態を親で持ち、更新関数（`setState`）を Props として子に渡すのが定番。

---

## いろいろな値を渡せる

Props には文字列や数値だけでなく、さまざまなものを渡せる。

```tsx
<Item
  title="りんご"        // 文字列
  price={100}           // 数値
  tags={['果物', '赤']} // 配列
  user={{ id: 1 }}      // オブジェクト
  onClick={handleClick} // 関数（イベントハンドラ）
/>
```

特に **関数を Props で渡す**のは重要なパターン。
子で起きたイベント（クリックなど）を、親に伝えて処理してもらえる（子 → 親への通知）。

```tsx
// 親
<Button onClick={() => alert('clicked')} />

// 子
function Button({ onClick }) {
  return <button onClick={onClick}>押す</button>;
}
```

---

## children（子要素を受け取る）

タグで囲んだ中身は、特別な Props である `children` として受け取れる。

```tsx
// 親：タグの中に要素を入れる
<Card>
  <p>これが中身</p>
</Card>

// 子：children で受け取る
function Card({ children }) {
  return <div className="card">{children}</div>;
}
```

レイアウトや枠だけを用意して、中身を差し込みたいときに便利。

---

## デフォルト値

Props が渡されなかったときの初期値は、分割代入で指定できる。

```tsx
function Button({ label = 'OK' }) {
  return <button>{label}</button>;
}

<Button />            // → OK
<Button label="送信" /> // → 送信
```

---

## TypeScript での型付け

TypeScript では、Props の形を型で定義しておくと安全。

```tsx
type UserCardProps = {
  name: string;
  age: number;
  isAdmin?: boolean; // ? は任意
};

function UserCard({ name, age, isAdmin }: UserCardProps) {
  return <p>{name}</p>;
}
```

---

## State との違い

| | Props | [State](hooks/useState.md) |
| --- | --- | --- |
| 誰が持つ | 親から渡される | コンポーネント自身が持つ |
| 変更 | **不可**（読み取り専用） | 可（`setState` で更新） |
| 役割 | 外から受け取る値 | 内部で変化する値 |

> ざっくり「**外から渡される値が Props、自分で持って変化させる値が State**」。

---

## まとめ

- Props は **親 → 子へデータを渡す仕組み**（関数の引数のようなもの）
- **読み取り専用**で、子では書き換えない（単方向データフロー）
- 文字列・数値だけでなく、配列・オブジェクト・**関数**も渡せる
- タグの中身は `children` として受け取れる

「データは上から下へ、通知は下から上へ（関数を渡す）」という流れが React の基本なのだと理解できた。

---

## 参考

- [React 公式ドキュメント - Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)
