# Poisson Regression（ポワソン回帰）

カウント目的変数に対してポワソン（GLM）回帰を実行し、結果を視覚的に評価します。

## 入力

1. **Data**: 入力データセット（`Orange.data.Table`）
   - 目的変数は非負の整数（カウント）である必要があります。負や非整数が含まれる場合はエラーになります。
   - 説明変数は数値・カテゴリのいずれでも構いません。カテゴリ変数はウィジェット内部で自動的にワンホットエンコーディングされます。
   - メタ変数は`Predictions`出力に引き継がれます（Cluster Key や Exposure/Offset の参照にも使用可能）。

## 出力

1. **Coefficients**: 係数テーブル（`Orange.data.Table`）。
   - COEF、IRR（= `exp(coef)`）、標準誤差、P値、VIF、IMPORTANCE（|coef|）を含みます。
   - 正則化（L2）を用いた場合はP値/標準誤差は表示されません。

2. **Predictions**: 予測結果（`Orange.data.Table`）。
   - 入力データに `predicted_<target>` というメタ列が追加され、各行の予測平均カウントが入ります。既存メタのデータ型は保持されます。

3. **Model**: 学習済みのGLM Poissonモデルオブジェクト。`get_coefficients()`、`get_p_values()`、`get_standard_errors()`、`get_feature_names()`、`evaluate(...)` などのヘルパーを提供します。

## 機能の説明

Poisson Regressionウィジェットでは、変数選択、モデル/リンク関数の設定、Exposure/Offsetの取り扱い、カウントデータに特化した診断の確認ができます。

### コントロールエリア（左パネル）

1. **Data Variables**
   - **Features**: 説明変数（数値/カテゴリ）。カテゴリは `drop='first'` でワンホット化されます。
   - **Target (count)**: 必須のカウント変数（非負整数のみ）。
   - **Meta Variables**: 学習には使用しませんが出力に保持します。Exposure/OffsetやCluster Keyの参照にも利用できます。

2. **Model Type**
   - **Poisson Regression (GLM)** のみ利用可能です。

3. **Link Function**
   - `log（正準）`（推奨）、`identity`、`sqrt`。

4. **Exposure / Offset**
   - **Use Exposure**: 生のスケール（例: ばく露時間）の変数を選ぶと、ウィジェットが自動でlogをとりオフセットに加えます。
   - **Use Offset**: 既に対数スケールの値（例: `log(exposure)`）をそのままオフセットに用います。
   - 両方指定した場合は、二つを足し合わせたlogオフセットが使われます。

5. **Regularization**
   - `None`、`L2 (Ridge)`（強さ `α` は0.001–1.000）。正則化時はP値/SEは提供されません。

6. **Advanced Options**
   - **Fit intercept**: 切片を含めるか。
   - **Normalize features**: 学習前に特徴量を標準化。
   - **Robust variance (HC1)**: へテロスケダスティシティに頑健な標準誤差（HC1）。
   - **Cluster standard errors (by variable)**: Cluster Key（例: user_id, store_id）でクラスタ化した標準誤差を用います。係数の推定値は変わらず、SEのみが調整されます。繰り返し観測があるデータで推奨。極端にクラスタ数が少ない場合は推奨度が下がります。
   - **Calculate VIF (サンプリング計算)**: 設定行数（既定10,000行）までのサンプルでVIFを計算。
   - **Plot sample size (表示上限)**: プロット描画点の上限。

7. **Apply**
   - 設定変更後に押すと再学習・再描画します。

### メインエリア（右パネル）

1. **Model Performance**
   - **Deviance**（小さいほど良い）。
   - **Pseudo-R² (McFadden)**: `1 - ll_model / ll_null`（`llnull`を使用）。
   - **Log-Likelihood**, **AIC**, **BIC**。
   - **Dispersion**: Pearsonカイ二乗/自由度。1を大きく超える場合は過分散の可能性が高い（ロバストSEや負の二項への切替を検討）。

2. **Coefficients & IRR**
   - COEF、IRR（`exp(coef)`）、標準誤差、P値、VIF、IMPORTANCEの表。
   - 係数可視化プロット（切片は除外、符号別の色分け）。

3. **Predicted vs Actual (Counts)**
   - 実測カウント（横軸）と予測カウント（縦軸）の散布図。対角線に近いほど適合が良い。

4. **Residual Diagnostics**
   - **Residuals vs Fitted**（ピアソン残差 vs 予測平均）。
   - **Normal Q-Q**（標準化残差のQ-Qプロット）。
   - **Scale-Location**（`sqrt(|standardized residuals|)` vs 予測）。
   - **Residuals vs Leverage**（Cook距離の等高線付き、レバレッジは最大1万行で計算）。

5. **Calibration / Gain**
   - **Calibration by Predicted Quantile**: 予測分位別の実測平均と予測平均の比較。
   - **Cumulative Gain**: 予測値でソートしたとき、上位からの実測カウント累積シェア。

## 詳細ロジック

### データ前処理

1. **ドメイン再構築**: UIで選んだFeatures/Target/Metasに基づいて再構築。
2. **カテゴリエンコード**: `drop='first'`, `handle_unknown='ignore'`のワンホット。
3. **バリデーション**: 目的変数が非負整数かを厳格に検証（違反時はエラー）。
4. **オフセット構築**: Exposure（logを自動適用）とOffset（log済み）を合算してオフセットベクトルを生成。
5. **欠損の除去**: 特徴量・目的変数・オフセットに非有限がある行は除外。
6. **正規化**: 有効化時は特徴量を標準化（切片はstatsmodels側で適切に扱う）。

### 学習

- `statsmodels.api.GLM` の Poisson を使用。切片は `sm.add_constant` で付与。
- 正則化（L2）を選択時は `fit_regularized` を試行（この場合のP値/SEは提供しない）。
- ロバスト/クラスタ標準誤差は `cov_type='HC1'` または `cov_type='cluster'`（groups指定）で計算。失敗時は非ロバストにフォールバックし警告。
- VIFは（必要に応じてエンコード後の）設計行列に対してサンプリング計算。

### 評価

- 予測値は平均カウント `E[y|x]`（オフセット指定時は単位曝露あたり）。
- 残差はピアソン残差 `(y - mu) / sqrt(mu)`。
- Pseudo-R² (McFadden) は `llnull` を使用。レバレッジは最大1万行でQRベースにより算出。

## 使用例

1. カウントの目的変数を持つデータを読み込む（例: イベント件数）。目的変数が非負整数であることを確認。
2. Features を選び、Target (count) を設定。必要なら Meta を選択。
3. 観測ウィンドウが行ごとに異なる場合は Exposure/Offset を設定。
4. リンク関数（推奨: log）や正則化、ロバスト/クラスタSEなどを必要に応じて設定。
5. Apply を押して学習。パフォーマンス指標、係数/IRR、残差診断、Calibration/Gain を確認。
6. `Coefficients` 出力で係数統計を、`Predictions` 出力で行ごとの予測カウントを確認。

## 注意事項

- 正則化（L2）時はP値/標準誤差は提供されません。
- Cluster SE を使う場合は、1クラスタに複数観測がある意味のあるキーを選ぶこと（クラスタ数が少なすぎると推測が不安定）。
- 強い過分散があるときは、準ポアソンや負の二項モデルの検討が必要な場合があります（本ウィジェットには未実装）。
