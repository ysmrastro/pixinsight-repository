---
layout: single
title: "PixInsight Scripts"
---

[English](en/)

PixInsight 用の自作スクリプトを公開しています。

## インストール

PixInsight の **Resources > Updates > Manage Repositories** で以下の URL を追加してください：

```
https://ysmrastro.github.io/pixinsight-scripts/
```

<div markdown="0">
<div class="script-cards-row">
  <a href="{{ site.baseurl }}/manual-image-solver/" class="script-card">
    <img src="{{ site.baseurl }}/assets/images/manual-image-solver/05_stars_registered.jpg" alt="Manual Image Solver">
    <h3>Manual Image Solver</h3>
    <p>手動プレートソルブツール。自動ソルブが失敗した画像に対し、組み込みカタログから星を選択して WCS を取得します。</p>
  </a>
  <a href="{{ site.baseurl }}/split-image-solver/" class="script-card">
    <img src="{{ site.baseurl }}/assets/images/split-image-solver/main-dialog.jpg" alt="Split Image Solver">
    <h3>Split Image Solver</h3>
    <p>広角星野写真を分割してプレートソルブし、WCS を統合して元画像に適用します。ImageSolver では対応できない超広角画像の SPCC 実行に対応。</p>
  </a>
  <a href="{{ site.baseurl }}/meteor-composer/" class="script-card">
    <img src="{{ site.baseurl }}/assets/images/meteor-composer/card.jpg" alt="Meteor Composer">
    <h3>Meteor Composer</h3>
    <p>一晩分の連番画像から流星を自動検出し、人の目で採否を判断して流星群コンポジットを生成します。何も自動では捨てません。</p>
  </a>
</div>
</div>

## 支援について

スクリプトは無料で公開しています。開発の継続を応援いただける方は [GitHub Sponsors](https://github.com/sponsors/ysmr3104) からご支援いただけます。
