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

# SaaS (Software as a Service) の発展と変化

SaaS業界が発展するにつれ、要求される機能や品質も変化している

## 個人・中小企業などスモールビジネス向け

- 差別化につながる機能
- 安心して長期利用できるランニングコスト

## 大企業や行政などエンタープライズ向け

- 誰でも使える明快さ
- 上場企業も安心して使えるコンプライアンス
- _組織管理_  
  大規模で階層的な組織を効率的かつ柔軟に管理できる

---
layout: two-cols-header
layoutClass: "!grid-rows-[60px_1fr] grid-cols-[360px_1fr]"
---

# SaaSの組織管理

::left::

## スモールビジネス

### データは _少量_ で構造も _シンプル_

<img src='/org-small-business.svg' style="height: 100px; margin-bottom: 30px;" />

### _<tabler-browser />GUI_ から登録したい

<img src='/form.svg' style="height: 155px" />

::right::

## エンタープライズ

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
▶ 修正作業は多大な時間と労力を伴う

- 修正する箇所(セル)が不明瞭
- 修正する理由が不明瞭  
  他のシートと依存関係がある場合は特に難しい  

## 繰り返される再入稿

全てのエラーが一度に返却されない  
▶ 何度も修正と再入稿を繰り返す

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

<img src="/dep.svg" />

---
layout: cover
---

## 解決策
# fp-tsによるエラー合成

::icon::

<fp-ts />

---
layout: two-cols-header
layoutClass: "!grid-rows-[90px_1fr]"
---

# TypeScriptのエラーハンドリング

まずはよく知られたエラーハンドリングの手法をおさらい

::left::

## ユーザー定義の例外をthrowする

```ts
class MyError extends Error {
  constructor(message) {
    super(message);
    this.name = "MyError";
  }
}
```

::right::

## Result型を返す

```ts
type Result<T, E> = Ok<T> | Err<E>;

type Ok<T> = Readonly<{
  ok: true;
  val: T;
}>;

type Err<E> = Readonly<{
  ok: false;
  err: E;
}>;
```

---

### TypeScriptのエラーハンドリング①
# ユーザー定義の例外をthrowする

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
- 古くからある実績ある手法

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[520px_1fr]">

```ts
if (err instanceof ParseError) {
  console.error(err.row)
}
```

<div>

`instanceof` 演算子により  
エラーを識別できる

</div>
</div>

---

# 😭 「ユーザー定義の例外をthrowする」場合の悩み

## 複数のエラーを同時に伝搬しづらい

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[490px_1fr]">

```ts
const parseUserRow = (cells: string[]) => ({
  id: parseUserId(cells[0]),        // 💥throw err
  name: parseUsername(cells[1]),    // 👋bye err
  birthday: parseBirthday(cell[2]), // 👋bye err
});
```

<div class="-my-3">

ある行のパース時に  
複数のセルのパースに失敗

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
# Result型を返す

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[500px_1fr]">

```ts
type Result<T, E> = Ok<T> | Err<E>;

type Ok<T> = Readonly<{ ok: true; val: T; }>;

type Err<E> = Readonly<{ ok: false; err: E; }>;
```

<div>

<ruby>判別可能な<rt>Discriminated</rt></ruby>Union型を用いて  
成功した場合と失敗した場合を表現

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[500px_1fr]">

```ts
declare const result: Result<string, ParseError>;
if (result.ok) {
  console.log(result.val);
} else {
  // OK
  console.error(result.err);
  // Property 'val' does not exist...
  console.log(result.val);
}
```

<div>

- 失敗する可能性を型で表現できる
- `ok` プロパティを見れば  
  型の絞り込みができる

</div>
</div>

---

# 👏 Result型なら複数のエラーを同時に伝搬できる 

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[450px_1fr]">

```ts
declare const parseUserId:
  (v: string) => Result<string, ParseError>;

declare const parseUsername:
  (v: string) => Result<string, ParseError>;
```

<div class="-my-3">

それぞれのセルのパース関数が `Result<string, ParseError>` を返す

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[450px_1fr]">

```ts
const parseUserRow = (cells: string[]) => ({
  id: parseUserId(cells[0]),     // Err
  name: parseUsername(cells[1]), // Err
});
```

<div class="-my-3">

IDと名前の両方のセルで  
パースに失敗した場合も...

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[450px_1fr]">

```ts
console.log(parseUserRow(['bad', 'bad']));
// {
//   id: { err: ... },
//   name: { err: ... },
// }
```

<div class="-my-3">

👏両方のセルのエラーを伝搬できる

</div>
</div>

---

# エラーを合成したい

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[400px_1fr] mb-8">

<div>

```ts
// セル
const id: Result<string, ParseError>;
const name: Result<string, ParseError>;

// 行
const row: Result<
  { id: string; name: string; },
  ParseError[],
>;
```
</div>

<div>

## 各セルを行へ合成したい

それぞれの行について  

❌ 1つ以上のセルの検証に失敗したら  
その行自体の検証を失敗としたい

✅ 全てのセルの検証に成功したら  
その行自体の検証を成功としたい

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[400px_1fr] mb-8">

<div>

```ts
const errs: ParseError[] = [];
if (!id.ok) errs.push(id.err);
if (!name.ok) errs.push(name.err);
if (errs) return errs;
// ...
```
</div>

<div>

## 😭 自前実装はつらい

そんな時に <fp-ts /> fp-ts

</div>
</div>

---

### fp-tsによるエラー合成
# オブジェクトのエラー合成

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[500px_1fr]">

```ts
import * as AP from 'fp-ts/Apply';
import * as A from 'fp-ts/Array';
import * as E from 'fp-ts/Either';
const ap = E.getApplicativeValidation(
  A.getSemigroup<string>(),
);
```

<div>
  
Leftに積まれたParseError[]を結合するapを定義

</div>
</div>

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[500px_1fr] mb-4">
<div>

```ts
const id: E.Either<string, ParseError[]>;
const name: E.Either<string, ParseError[]>;

type User = { id: string; name: string; };

// E.Either<ParseError[], User>
const eitherUser = AP.sequenceS(ap)({ id, name });
```

</div>
<div>

先ほど定義したapを用いて
各プロパティのResultを合成
成功: User
失敗: ParseError[]

</div>
</div>

---

### fp-tsによるエラー合成
# 配列のエラー合成

<div class="grid grid-cols-2 mb-4 gap-4 !grid-cols-[500px_1fr] mb-4">

<div>

```ts
const rows: Array<E.Either<ParseError[], User>>;

// E.Either<ParseError[], User[]>;
const eitherUsers = A.sequence(ap)(rows);
```

</div>

</div>

---

# 他ライブラリとの比較

## neverthrow/option-t

Result/Optionを提供することに特化  
合成機能は提供していない

## Effect

将来的なfp-tsの乗り換え先として有力

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
# プロジェクトで利用するものを限定する

- pipe/flow
- Either/Option
- Task/TaskEither

---

## チームでfp-tsを利用するために②
# ペアプロ・モブプロの活用

---

## チームでfp-tsを利用するために③
# 社内向けレシピ集

