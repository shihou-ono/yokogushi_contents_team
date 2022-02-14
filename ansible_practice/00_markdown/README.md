---
marp: true
theme: apc_confidential
size: 16:9
---
<!--
class: title
-->

# Marp for VS Codeを使ってみよう

## Date: 2021/06/21

---

<!--
class: chapter
paginate: false
-->

# Marp for VS Codeとは？

---

<!--
class: slide
paginate: true
-->

# 1. Marp for VS Codeとは？

- Markdownから、プレゼンスライドを生成してくれるツール
  - 普段使いのテキストエディタで、箇条書きをするだけでスライドになる
    - テキストがそのままパワポになる！
    - この資料も``Marp for VS Code``で作ってあります
  - 中身がテキストと変わらないので差分がわかりやすい
  - Gitなどでバージョン管理
- __Ansibleなどの自動化ツールとの相性がよい__

---

# 1. Marp for VS Codeとは？

- 実際に使ってみよう！
  1. 以下のドライブにあるフォルダをローカルにダウンロードし、解凍する
     - フォルダ名: marp_template_apc
     - https://drive.google.com/drive/folders/1yr3lmAqbiFG_t43N-70P58YJphqvBSmn
  2. ダウンロードしたフォルダをVS Codeで開く
     - フォルダをVS Codeのアイコンにドラッグ&ドロップするとよい
  3. 拡張機能``Marp for VS Code``をインストール（[拡張機能のインストール方法はコチラ][1]）
  4. setting.jsonに以下の記載をする（[setting.jsonの編集方法はコチラ][2]）

     ```json
         "markdown.marp.themes": [
             "./marp-themes/apc_confidential.css"
         ]
     ```

---

# 1. Marp for VS Codeとは？

![bg right vertical 80%](./images/vscode_screen.drawio.svg)

- 実際に使ってみよう！（つづき）

  5. ``sample_slide.md``をアクティブにしてアイコン(右図)を選択し、スライドのフォーマットが反映されていることを確認する
  6. ``sample_slide.md``をアクティブにしてF1を押す
  7. ``Marp: Export slide deck``を選択し、PDF出力するファイル名を選択すると
  PDFファイルが出力される
     - 注)PDFはVS Codeで開くと文字化けします

---

<!--
class: chapter
paginate: false
-->

# Draw io Integrationとは？

---

<!--
class: slide
paginate: true
-->

# 2. Draw io Integrationとは？

- 以下のような図を簡単にお絵かきをすることができるサービス
  - フローチャート
  - ネットワーク図
  - ER図
- __Marp for VS Codeと組み合わせることが可能__

---

# 2. Draw io Integrationとは？

![bg right vertical 30%](./images/sample.drawio.svg)

- 実際に使ってみよう！
  1. VS Codeで``marp_template_apc``を開く
  1. 拡張機能``Draw.io Integration``をインストール
  1. ``./images/sample.drawio.svg``を編集して保存する(ctrl + s)
  1. ``Marp for VS Code``で右図の状態を確認

---

<!--
class: chapter
paginate: false
-->

# 参考サイト

---

<!--
class: slide
paginate: true
-->

# 参考サイト

- 【VS Code + Marp】Markdownから爆速・自由自在なデザインで、プレゼンスライドを作る
  - https://qiita.com/tomo_makes/items/aafae4021986553ae1d8
- VSCodeでDraw.ioが使えるようになったらしい！
  - https://qiita.com/riku-shiru/items/5ab7c5aecdfea323ec4e

---

<!--
class: backcover
paginate: true
-->

[1]: https://blog-and-destroy.com/21376
[2]: https://blog-and-destroy.com/25199