---
marp: true
paginate: true
math: katex
size: 16:9
theme: gaia
backgroundColor: white
footer: ""
style: |
    :root {
        --h2-font-size: 40px;
        --h3-font-size: 35px;
        --normal-font-size: 24px;
    }

    * {
        margin: 0;
        padding: 0;
    }

    h2 {
        font-size: var(--h2-font-size);
        padding-top: 0.3em;
        padding-bottom: 1.0em;
    }

    h3 {
        font-size: var(--h3-font-size);
        padding-top: 0.3em;
        padding-bottom: 0.6em;
    }

    p,li {
        font-size: var(--normal-font-size);
        line-height: 1.5;
        padding-top: 0.2em;
        padding-bottom: 0.3em;
    }

    section {
        padding: 60px 80px;
    }

    table {
        font-size: 18px;
        border-collapse: collapse;
        margin: 0 auto;
        max-width: 90%;
        margin-top: 0.4em;
        margin-bottom: 0.5em;
    }

    th,td {
        padding: 4px 8px;
        border: 1px solid #ccc;
    }

    .center-title {
        height: 100%;
        text-align: center;
        display: flex;
        flex-direction: column;
        justify-content: center;
    }

---

<section class="center-title">

## 基礎的なGANの導入とScalarGANによる画像生成の紹介

青井研 M2 木村航也

</section>

---

### 導入：回帰から見る機械学習

機械学習の基本的な考え方の一つに「回帰」がある
回帰とは、データ間の関係性を数式としてモデル化する方法

例：身長と体重の関係を、一次関数 $y = ax + b$ で近似する

| 身長 (cm) | 体重 (kg) |
|----------|-----------|
| 150      | 45        |
| 160      | 50        |
| 170      | 58        |
| 180      | 65        |

身長 $x$ を入力として、体重 $y$ を出力するような関係を学習する

（図：散布図と、おおまかにフィットする直線）

---
### 導入：回帰式の学習

仮に $y = 0.3x + 10$ としてみると、予測は以下の通り：

| $x$ (cm) | 実際の $y$ | 予測値 $0.3x + 10$ | 誤差 |
|---------|-------------|---------------------|------|
| 150     | 45          | 55                  | -10  |
| 160     | 50          | 58                  | -8   |
| 170     | 58          | 61                  | -3   |
| 180     | 65          | 64                  | +1   |

誤差の大きさを評価し、より良い $a$, $b$ を探索していく
誤差の指標例：二乗誤差

$$
\text{Loss} = \sum_{i=1}^n (y_i - (ax_i + b))^2
$$

（図：数本の直線候補と、予測とのズレのイメージ）

---

### 導入：画像生成とその技術的背景

近年、画像を自動で生成するAI技術が急速に発展  
AIによって、写実的・創造的な画像の生成が可能に  
主な応用例：
・イラスト・人物画像の生成
・画像の補完・修復
・スタイル変換など

代表的な画像生成モデル：
・Diffusionモデル（例：Stable Diffusion）
・Transformer系（例：DALL·E 2）
・**GAN（Generative Adversarial Network）**

---

### GANとは？

画像を騙すような“Generator”と“Discriminator”が競い合う構造。
例：手書き文字や人物画像生成などを紹介。

（画像：MNISTや有名な人物生成例など）

---

### DCGANの構造（全体像）

DCGANのモデル構成図を掲載し、  
GeneratorとDiscriminatorの流れを説明。

（画像：DCGANのブロック図）

---

### DCGANの数式①：Generatorの出力

Generatorはノイズ $z$ を入力に、畳み込み層を通して画像を出力する。

$$
G(z) = \text{ConvTranspose}(z)
$$

---

### DCGANの数式②：Discriminatorの出力

Discriminatorは画像 $x$ を入力に、真偽の確率を出力する。

$$
D(x) = \sigma(\text{Conv}(x))
$$

---

### DCGANの数式③：損失関数

GeneratorとDiscriminatorの目的関数は以下のように表される。

$$
\min_G \max_D V(D, G) = \mathbb{E}_{x \sim p_{\text{data}}}[\log D(x)] + \mathbb{E}_{z \sim p_z}[\log(1 - D(G(z)))]
$$

---

### CGANとは？条件付きGANの紹介

ラベル $y$ を用いて生成をコントロールする手法。  
例：手書き数字のラベルを与えて特定の数字を生成。

（画像：CGANによる数字ラベル付き生成例）

---

### CGANの仕組みと数式

GeneratorとDiscriminatorの両方に条件 $y$ を与える。

$$
G(z, y), \quad D(x, y)
$$

$$
\min_G \max_D V(D, G) = \mathbb{E}_{x, y}[\log D(x, y)] + \mathbb{E}_{z, y}[\log(1 - D(G(z, y), y))]
$$

---

### Scalar GANとは？新しい制御の発想

クラスラベルの代わりに、連続値（スカラー）で生成を制御する試み。  
例：3〜5角形を学習し、間の4.2角形を出力。

（画像：3角〜5角の形状の遷移イメージ）

---

### データジェネレーターの紹介（p5.js）

p5.js を使って自作した形状生成ツール。  
多角形の頂点数や面積をパラメータとして調整可能。

（画像：https://github.com/koya-kimura/p5-shape-table-generator より）

---

### Scalar GANの学習例①：多角形の補間生成

例：3角形と5角形の間を連続的に補完した画像群を掲載。

（画像：連続補間の生成結果サンプル）

---

### Scalar GANの学習例②：スカラー値による変化

入力するスカラー値に応じて形状が連続的に変化する例を提示。

（画像：スカラー値 vs 生成形状のプロット）

---

### Scalar GANの可能性と意義

・ラベルなしで連続制御が可能  
・中間的な形状・意味のある生成ができるかもしれない

---

### 今後の展望

・より高精度なデータ生成への改善  
・他の情報（面積、歪みなど）との多次元制御への拡張

---

### 全体のまとめ

画像生成の基礎から、DCGAN、CGAN、そしてScalar GANへの展開を紹介。  
連続値による制御という新しい視点が持つ可能性について発表した。