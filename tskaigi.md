---
theme: ./theme
background: "#ffffff"
title: 正確性と効率性を両立するfp-tsのチーム活用術
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
   SaaSのエンタープライズ対応と<br />
   <vscode-icons-file-type-excel2 />Excel一括入稿機能の重要性
1. 課題<br />
   <vscode-icons-file-type-excel2 />Excel一括入稿機能で<br />
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
# SaaSのエンタープライズ対応と<br />Excel一括入稿機能の重要性

::icon::

<tabler-users class='mr-3' />

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
layoutClass: "grid-cols-[360px_1fr]"
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
layout: cover
---

## 課題
# Excel一括入稿機能で<br />正確かつ効率的に検証したい

::icon::

<vscode-icons-file-type-excel2 class='mr-3' />

---
layout: two-cols-header
layoutClass: "!grid-cols-[360px_1fr]"
---

# _ユーザー_ から見た一括入稿のつらさ

::left::

<img src=/bulk-import-flow.svg />

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

# 表形式データの検証の流れ

<img src="/parse-flow.svg" />

---
layout: two-cols-header
layoutClass: "!grid-rows-[90px_1fr] !grid-cols-[480px_1fr]"
---

### 一括入稿の難しさ①
# エラーの一括返却

例) 異なるステップで失敗する3行を含む表を入稿

::left::

<img src="/parse-flow-error.svg" />

::right::

### 理想

各ステップで検証に成功した行のみ  
次の検証ステップに進めて  
エラーを極力まとめて出力したい

### ナイーブな実装の場合

各ステップで不正が検出されたら  
例外を発生させる

例)「不正な日付」で中断され  
「重複ID」や「店舗IDの不存在」を  
含めて一度に返却できない

---
layout: two-cols-header
layoutClass: "!grid-rows-[90px_1fr] !grid-cols-[480px_1fr]"
---

### 一括入稿の難しさ②
# シート間の依存関係

::left::

<img src="/dep.svg" />

::right::

## 複雑な参照

組織の階層構造を表現するために  
セル、行、シート間に依存関係がある

---
layout: cover
---

## 解決策
# fp-tsによるエラー合成

::icon::

<fp-ts />

---

### TypeScriptのエラーハンドリング①
# カスタムエラー

<div class="grid grid-cols-2 mb-4 gap-4">

```ts
class MyError extends Error {
  constructor(message) {
    super(message);
    this.name = "MyError";
  }
}
```

</div>

---

### TypeScriptのエラーハンドリング②
# Result型

<div class="grid grid-cols-2 mb-4 gap-4">

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

</div>
