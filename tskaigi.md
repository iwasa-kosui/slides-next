---
theme: ./theme
background: "#ffffff"
title: 複雑なビジネスルールに挑む：正確性と効率性を両立するfp-tsのチーム活用術
info: |
  ## fp-tsの柔軟な関数合成・エラー合成機能
  この発表では、複数の医療系SaaSを展開するカケハシにて、エンタープライズな顧客の組織階層を管理・配信する基盤システム「OGAS」の事例を紹介します。「Excel入稿ファイルからツリー形式のデータ構造を組み立てる」という複雑な要件に対し、fp-tsの柔軟な関数合成・エラー合成機能で立ち向かい、検証ロジックの正確さと効率性を両立させることができました。
  ## fp-tsをチームで活用するために
  さらに、この発表ではfp-tsをチームで活用するための工夫も紹介します。fp-tsは日本語の情報が少なく、neverthrowなどと比べて抽象度が高いため、しばしば敬遠されることがあります。しかし、OptionやEitherだけではなく、これらを柔軟に合成できるpipeやflow、Validationなどの仕組みを活用することで、複雑なビジネスロジックに立ち向かうことができます。私達は、一日二回のモブプロや、すぐに使える社内レシピ集などを通じ、幅広い背景を持つメンバーが活躍できる環境を実現しました。
class: text-center
highlighter: shiki
drawings:
  persist: false
mdc: true
---

## 複雑なビジネスルールに挑む：

# _**正確性**_ と _**効率性**_ を両立する<br/><fp-ts />**fp-ts**のチーム活用術

@kosui

---
layout: two-cols
layoutClass: "!grid-cols-[300px_1fr]"
---

<Avatar />

## <tabler-brand-x /> [@kosui_me](https://x.com/kosui_me)

## <tabler-brand-github /> [@iwasa-kosui](https://github.com/iwasa-kosui)

## <tabler-home /> https://kosui.me

::right::

# 自己紹介 (1分)

## kosui (岩佐 幸翠)

株式会社カケハシにて共通基盤を開発

---

# この発表のねらい

## <fp-ts /> fp-ts で複雑性と闘う

複雑なビジネスロジックでも **_正確性_** と **_利便性_** を両立するために  
エラーや関数を合成できる **_fp-ts_** が便利！

実際に<vscode-icons-file-type-excel2 />一括入稿機能の開発で遭遇した悩みと解決策を紹介

<img src='/aim.svg' class='mt-2' />

## <fp-ts /> fp-ts をチームで使いこなすために 

関数型プログラミング初学者の多いチームをどのようにスケールさせるか

---

# 目次

1. 背景  
   SaaSのエンタープライズ対応と
   <vscode-icons-file-type-excel2 />Excel一括入稿機能の重要性
1. 課題<br />
   <vscode-icons-file-type-excel2 />Excel一括入稿機能で 
   _正確_ かつ _効率的_ に検証したい
1. fp-tsによるエラー合成
1. fp-tsのチーム活用

<style scoped>
li {
  font-size: 24px;
}
</style>

---
layout: cover
---

## 背景
# SaaSのエンタープライズ対応と<br />ファイル一括入稿機能の重要性

::icon::

<vscode-icons-file-type-excel2 />

---
layout: two-cols-header
layoutClass: "!grid-rows-[120px_1fr] !gap-4"
---

# SaaS (Software as a Service) の発展と変化

SaaS業界が発展するにつれ、要求される機能や品質も変化している

::left::

## 個人・中小企業向け

- 差別化につながる先進的な機能・品質
- 安心して長期利用できるプライシング

::right::

## 大企業・行政向け

- 誰でも使える明快さ
- 上場企業も安心して使えるコンプライアンス
- _組織管理_  
  大規模で階層的な組織を  
  効率的かつ柔軟に管理できる

---
layout: two-cols-header
layoutClass: "!grid-rows-[60px_1fr] grid-cols-[360px_1fr]"
---

# SaaSの組織管理

::left::

## 個人・中小企業向け

### データは _少量_ で構造も _シンプル_

<img src='/org-small-business.svg' style="height: 100px; margin-bottom: 30px;" />

### _<tabler-browser />GUI_ から登録したい

<img src='/form.svg' style="height: 155px" />

::right::

## 大企業・行政向け

### データが _大量_ で _階層的_

<img src='/org-enterprise.svg' style="height: 100px; margin-bottom: 0px;" />

### _<vscode-icons-file-type-excel2 />ファイル_ から一括入稿したい

<img src='/excel-example.svg' />

---
layout: two-cols-header
layoutClass: "!grid-cols-[360px_1fr]"
---

# _ユーザー_ から見た<vscode-icons-file-type-excel2 />ファイル一括入稿のつらさ

::left::

<img src=/bulk-import-flow.svg style='width: 320px' />

::right::

## 不明瞭なフィードバック

エラーの原因が判別しにくい  
➡ 修正作業は多大な時間と労力を伴う

- 修正すべき箇所(セル)が不明瞭
- 修正すべき理由が不明瞭  
  データ型？使用不能文字？値の重複？  
  他のシートと依存関係がある場合は特に難しい  

## 繰り返される再入稿

全てのエラーが一度に返却されない  
➡ 何度も修正と再入稿を繰り返す

---
layout: cover
---

## 課題
# 表形式データの検証<br/>正確性と効率性を阻む２つの壁

::icon::

😭

---

# 表形式データの検証の3ステップ

<img src="/parse-flow.svg" />

---

### 表形式データ検証の壁①
# 表形式データのエラーハンドリング

<img src="/parse-flow-error.svg" />

::right::

## 理想

各ステップで検証に成功した行のみ  
次の検証ステップに進めて  
エラーを極力まとめて出力したい

## ナイーブな実装の場合

各ステップで不正が検出されたら  
例外を発生させ _処理を中断_ する

---

### 表形式データ検証の壁②
# 依存関係の解決

組織の階層構造を表現するために、ある行が他シートの行を参照する  
例) ユーザーの所属を表現するため店舗シートを参照

<Grid width='500px'>

![](/dep.svg)

<div>

😱検証済みの行を参照したいのに  
未検証の行を参照してしまった

➡ 型検査で未然に防ぎたい

</div>
</Grid>

---
layout: cover
---

## 解決策
# fp-tsによるエラー合成

::icon::

<fp-ts />

---
layout: two-cols-header
layoutClass: "!grid-rows-[120px_1fr]"
---

# TypeScriptのエラーハンドリング

まずはよく知られたエラーハンドリングの手法をおさらい

::left::

## ユーザー定義エラーをthrowする

```ts
class MyError extends Error {
  constructor(message) {
    super(message);
    this.name = "MyError";
  }
}
```

::right::

## Either型(Result型)を返す

```ts
type Either<E, A> = Left<E> | Right<A>;

type Left<T> = Readonly<{
  tag: 'Left';
  left: T;
}>;

type Right<A> = Readonly<{
  tag: 'Right';
  right: A;
}>;
```

---

### TypeScriptのエラーハンドリング①
# ユーザー定義エラーをthrowする

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[520px_1fr]">

```ts
class ParseError extends Error {
  constructor(readonly row: number, msg?: string) {
    //                 ^^^ 行番号を持たせる
    super(msg);
    this.name = "ParseError";
  }
}
```

<div>

`Error` を拡張したクラスを定義する

- エラー原因の情報を追加できる

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[520px_1fr]">

```ts
throw new ParseError(1);
```

<div class='my-1'>

- 例外としてthrowする

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[520px_1fr]">

```ts
if (err instanceof ParseError) {
  console.error(err.row)
}
```

<div class='my-1'>

- `instanceof` 演算子により  
エラーを識別できる

</div>
</div>

---

# 😭 例外をthrowする場合の悩み

## 複数のエラーを同時に伝搬しづらい

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[490px_1fr]">

```ts
const parseUserRow = (cells: string[]) => ({
  id: parseUserId(cells[0]),        // 💥throw err
  name: parseUsername(cells[1]),    // 👋bye err
  birthday: parseBirthday(cell[2]), // 👋bye err
});
```

<div>

`parseUserId` が例外を投げてしまうと  
他のセルの検証エラーをクライアントへ  
返せないまま処理が中断

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[490px_1fr]">

```ts
const errors: Error = [];
let id: string;
try {
  id = parseUserId(cells[0]);
} catch(e) {
  errors.push(e);
}
// ...
```

<div class="-my-3">

try...catch文で頑張れば出来なくはない  

しかし、流石にこれはつらい

</div>

</div>

<style scoped>
pre {
  height: 100%;
}
</style>

---

### TypeScriptのエラーハンドリング②
# Either型(Result型)を返す

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[525px_1fr]">

```ts
type Either<E, A> = Left<E> | Right<A>;

type Left<T> = Readonly<{ tag: 'Left'; left: T; }>;

type Right<A> = Readonly<{ tag: 'Right'; right: A; }>;
```

<div>

<ruby>判別可能な<rt>Discriminated</rt></ruby>Union型を用いて  
成功した場合と失敗した場合を表現

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[525px_1fr]">

```ts
declare const either: Either<ParseError, string>;
if (either.tag === 'Right') {
  console.log(either.right);
} else {
  // OK
  console.error(either.left);
  // Property 'right' does not exist...
  console.log(either.right);
}
```

<div>

- 失敗する可能性を型で表現できる
- `tag` プロパティを見れば  
  型の絞り込みができる

</div>
</div>

---

# Either型を用いたエラーの伝搬

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[440px_1fr]">

```ts
declare const parseUserId:
  (v: string) => Either<ParseError, string>;

declare const parseUsername:
  (v: string) => Either<ParseError, string>;
```

<div class="-my-3">

それぞれのセルのパース関数が `Either<ParseError, string>` を返す

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[440px_1fr]">

```ts
const parseUserRow = (cells: string[]) => ({
  id: parseUserId(cells[0]),     // Left
  name: parseUsername(cells[1]), // Left
});
```

<div class="-my-3">

IDと名前の両方のセルで  
パースに失敗した場合も...

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[440px_1fr]">

```ts
console.log(parseUserRow(['bad', 'bad']));
// {
//   id: { left: ... },
//   name: { left: ... },
// }
```

<div class="-my-3">

👏両方のセルのエラーを伝搬できる

</div>
</div>

---

# それぞれのセルを行へ合成したい

![](/col-to-row.svg)

---

# それぞれのセルを行へ合成したい

<Arrow x1="490" y1="190" x2="520" y2="190" width=2 />

### エラーとなるセルが含まれる場合

<div class="grid grid-cols-2 items-center mb-4 gap-4 !grid-cols-[420px_300px] gap-x-[60px]">

```ts
{
  id: left([new ParseError(1, 'ID')]),
  name: left([new ParseError(1, '名前')]),
  storeId: right(22),
};
```

```ts
left([
  new ParseError(1, 'ID'),
  new ParseError(1, '名前'),
]);
```

</div>

<Arrow x1="490" y1="380" x2="520" y2="380" width=2 />

### エラーとなるセルが無い場合

<div class="grid grid-cols-2 items-center mb-4 gap-4 !grid-cols-[420px_300px] gap-x-[60px]">

```ts
{
  id: right(1),
  name: right('田中'),
  storeId: right(22),
};
```

```ts
right({
  id: right(1),
  name: right('田中'),
  storeId: right(22),
});
```

</div>

---

# それぞれのセルを行へ合成したい

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[380px_1fr]">

```ts
const errs: ParseError[] = [];
if (isLeft(id)) {
  errs.push(id.left);
}
if (isLeft(name)) {
  errs.push(name.left);
}

if (errs) {
  return left(errs);
}

assert(isRight(id));
assert(isRight(name));
return right({
  id: id.right,
  name: name.right;
})
```

<div>

## 自前実装

### いずれかのセルが `Left` の場合

エラーを取り出して配列に詰め
`Left<ParseError[]>` として返す

### 全てのセルが `Right` の場合

`Right<{ id: number, ... }>`
として返す

## ライブラリに頼りたい

😭 自分でやりたくはない

</div>
</div>

---

<Arrow x1="420" y1="180" x2="450" y2="180" width=2 />

### それぞれのセルを行へ合成したい
# fp-tsによるオブジェクトのエラー合成

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[350px_350px] gap-x-[60px]">

```ts
const cells = {
  id: left([new ParseError(1)]),
  name: left([new ParseError(1)]),
};
```

```ts
type Row = Either<ParseError[], {
  id: string,
  name: string,
}>;
```

</div>

<Grid width='420px'>

```ts
import * as AP from 'fp-ts/Apply';
import * as A from 'fp-ts/Array';
import * as E from 'fp-ts/Either';
const ap = E.getApplicativeValidation(
  A.getSemigroup<string>(),
);
```

<div>
  
1. `Left` の `ParseError[]` を  
結合する関数 `ap` を定義

</div>
</Grid>

<Grid width='420px'>
<div>

```ts
// 行へ合成
const row: Row = AP.sequenceS(ap)(cells);
```

</div>
<div class='-my-2'>

2. 先ほど定義した  `ap` を用いて  
`Record<string, Either<>>` を合成できる

</div>
</Grid>

---

# それぞれの行をシートへ合成したい

![](/row-to-sheet.svg)

---

<Arrow x1="495" y1="240" x2="525" y2="240" width=2 />

### fp-tsによるエラー合成
# 配列のエラー合成

## やりたいこと

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[425px_350px] gap-x-[60px]">

```ts
const rows = [
  left([new ParseError(1)]),
  right({
    id: 2,
    name: '田中',
  }),
  left([new ParseError(3)]),
];
```

```ts
const sheet = left([
  new ParseError(1),
  new ParseError(3),
]);
```

</div>

## 実現方法

<Grid width='420px'>
<div>

```ts
// 行へ合成
const sheet: Row = A.sequence(ap)(rows);
```

</div>
<div class='-my-2'>

先ほど定義した  `ap` を用いて  
`Either` の配列を合成できる🎉

</div>
</Grid>

---

### fp-tsによるエラー合成
# セル→行→シートまで一気通貫で合成

<Grid>

```ts
import { pipe } from 'fp-ts/function';
pipe(
  [
    {
      id: left([new ParseError(1)]),
      name: left([new ParseError(1)]),
    },
    {
      id: left([new ParseError(1)]),
      name: left([new ParseError(1)]),
    },
  ],
  A.map(AP.sequenceS(ap)), // 1. 行へ合成
  A.sequence(ap), // 2. シートへ合成
);
```

<div>

1. 各セルで構成されるオブジェクト ➡ 行へ
1. 行の配列 ➡ シートへ

合成処理を簡潔に表現できる😘

### 顧客の価値に直結

冒頭でも述べた通り表形式データの検証は  
極力 _一度に_ 全てのエラーを返すことが  
顧客体験のために重要

</div>
</Grid>

---
layout: cover
---

# fp-tsのチーム活用

::icon::

<fp-ts />

---

# fp-tsの難しさ

## コンセプト

関数型プログラミングのためのユーティリティ

## 難点

- 非常に抽象度が高い  
  ドキュメントも抽象度が高い
- 日本語の情報が少ない
- 業務での実用例の解説が少ない

---

## チームでfp-tsを利用するために①
# [WIP] プロジェクトで利用するものを限定する

- pipe/flow
- Either/Option
- Task/TaskEither

WIP

---

## チームでfp-tsを利用するために②
# [WIP] ペアプロ・モブプロの活用

WIP

---

## チームでfp-tsを利用するために③
# [WIP] 社内向けレシピ集

WIP