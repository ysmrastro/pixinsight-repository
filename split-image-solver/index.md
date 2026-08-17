---
layout: single
title: "Split Image Solver"
---

[English](en/) \| [GitHub リポジトリ](https://github.com/ysmr3104/split-image-solver)

広角星野写真を分割してプレートソルブし、統合した WCS 座標情報を元画像に適用する PixInsight スクリプトです。PixInsight の ImageSolver では対応できない超広角な星空画像に対応します。

> **注意**: Split ソルブでは画像中心から離れるほど歪みが大きくなるため、周辺部の精度が必要な場合（AnnotateImage での利用等）は [Manual Image Solver](../manual-image-solver/) の利用をご検討ください。Split Image Solver は AnnotateImage の完璧な精度を求めるものではなく、ImageSolver 単体では不可能な超広角画像での SPCC 実行を可能にするツールです。

## スクリーンショット

### メインダイアログ

![メインダイアログ]({{ site.baseurl }}/assets/images/split-image-solver/main-dialog.jpg)

左パネルに画像プレビューとグリッドオーバーレイがリアルタイム表示され、タイル分割の様子を確認できます。スキップされるエッジタイルは暗いオーバーレイと「skip」ラベルで表示されます。右パネルには機材選択、グリッド設定、エッジスキップ、Sesame 天体名検索などが並びます。

### Settings ダイアログ

![Settings ダイアログ]({{ site.baseurl }}/assets/images/split-image-solver/settings-dialog.jpg)

左下の「Settings...」ボタンから開きます。ソルブモード（API / Local / ImageSolver）の切り替え、API キー、Python 環境の設定を行います。

## 主な機能

- **3 つのソルブモード**: astrometry.net API（デフォルト）、ローカル solve-field（Python）、ImageSolver（PixInsight 内蔵）
- **画像プレビュー＋グリッドオーバーレイ**: タイル分割の様子をリアルタイムで可視化、STF ストレッチ（None/Linked/Unlinked）対応
- **柔軟な分割**: 1x1（単一画像）から 12x8 まで、FOV に基づく「Recommended」ボタンでワンクリック最適グリッド設定
- **エッジスキップ**: 上下左右のタイル行/列をスキップ（星景写真で地上部分を除外するのに便利）
- **高精度**: SIP 歪み補正対応、WCSFitter による制御点フィット
- **部分ソルブ対応**: 一部のタイルのソルブに失敗しても、成功したタイルから WCS を全体に適用
- **2 パスリトライ**: 失敗タイルを隣接成功タイルの WCS ヒントで自動再試行
- **オーバーラップ検証**: 隣接タイルの重複領域で WCS 整合性を自動チェック
- **機材 DB**: カメラ/レンズの自動認識、焦点距離・ピクセルピッチ自動入力
- **魚眼レンズ対応**: equisolid/equidistant/stereographic 投影に対応、タイルごとのスケール補正
- **Sesame 天体名検索**: 天体名から RA/DEC を自動入力
- **SPFC 互換**: SubframeSelector、SPCC 等と連携

## ソルブモード

| モード | 説明 | 必要な環境 | 精度 | Split |
|--------|------|-----------|------|-------|
| **API**（デフォルト） | astrometry.net API でソルブ | API キーのみ（Python 不要） | 良好 | 対応 |
| **Local** | ローカル solve-field でソルブ | Python + solve-field + 星カタログ | 最高 | 対応 |
| **ImageSolver** | PixInsight 内蔵 ImageSolver でソルブ | 追加環境不要 | — | Single のみ |

精度の順: **Local > API > ImageSolver**。**ImageSolver モード**は API キーも Python も不要で、単一画像のプレートソルブに手軽に使えます。

## インストール

### リポジトリからインストール（推奨）

1. PixInsight を開く
2. **Resources > Updates > Manage Repositories** を開く
3. 以下のリポジトリ URL を追加:
   ```
   https://ysmrastro.github.io/pixinsight-scripts/
   ```
4. **Resources > Updates > Check for Updates** を実行
5. SplitImageSolver をインストール

### 手動インストール

1. リポジトリを clone またはダウンロード:
   ```bash
   git clone https://github.com/ysmr3104/split-image-solver.git
   ```
2. `javascript/` 内の以下のファイルを PixInsight スクリプトディレクトリ（`SplitImageSolver/` フォルダ）にコピー:
   - `SplitImageSolver.js` / `astrometry_api.js` / `wcs_math.js` / `wcs_keywords.js` / `equipment_data.jsh` / `imagesolver_bridge.jsh`
3. PixInsight を再起動 → **Script > Astrometry > SplitImageSolver** から実行

## 使い方

### クイックスタート（単一画像ソルブ）

1. PixInsight で対象画像を開く
2. **Script > Astrometry > SplitImageSolver** を実行
3. 左下の **Settings...** ボタンでソルブモードと API キーを設定
4. Grid を **1x1 (Single)** のまま「Solve」をクリック
5. 完了後、画像に WCS が適用される

### 分割ソルブ（広角画像）

1. **カメラ/レンズ**を選択（FITS ヘッダーから自動認識される場合あり）
2. **Recommended** ボタンで FOV に基づく最適グリッドをワンクリック設定
3. 星景写真の場合、**Skip edges** で地上部分をスキップ（例: B:2 で下 2 行をスキップ）
4. 必要に応じて天体名を入力して RA/DEC を取得
5. 「Solve」をクリック

### パラメータ

| パラメータ | 説明 |
|-----------|------|
| Camera / Lens | カメラ・レンズ機種（ピクセルピッチ・焦点距離を自動入力） |
| Focal length | 焦点距離 (mm) |
| Pixel pitch | ピクセルピッチ (μm) |
| Grid | 分割グリッド（ColsxRows）、1x1 〜 12x8 |
| Overlap | タイル間オーバーラップ (px) |
| Skip edges | 上下左右のスキップするタイル行/列数 (T/B/L/R) |
| Object / RA / DEC | 天体名（Sesame 検索）・中心座標 |
| Downsample / SIP Order | ダウンサンプル設定・SIP 歪み補正次数（API モード） |

## トラブルシューティング

- **ソルブに時間がかかる / 失敗する**: RA/DEC ヒントを入力するとソルブ速度が大幅に向上します。焦点距離・ピクセルピッチも正確に入力してください。
- **一部タイルがソルブできない**: 地上風景や雲を含むタイルはソルブできません（正常動作）。2 タイル以上成功すれば WCS 統合が可能です。Pass 2 リトライで自動的に再試行されます。
- **WCS 精度が低い**: オーバーラップを増やす、SIP Order を上げる、グリッドを細かくする、などを試してください。周辺部の精度が必要な場合は [Manual Image Solver](../manual-image-solver/) をご検討ください。
- **Local モードで Python が見つからない**: Settings の Python パスが正しいか確認してください。

## ライセンス

[MIT License](https://github.com/ysmr3104/split-image-solver/blob/main/LICENSE)

## 支援について

スクリプトは無料で公開しています。開発の継続を応援いただける方は [GitHub Sponsors](https://github.com/sponsors/ysmr3104) からご支援いただけます。
