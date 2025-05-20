# Binary Analysis

`(アイコン: icons/interactive_classification.svg)`

ロジスティック回帰を用いた二項分類分析を行うウィジェットです。対話的に特徴量やモデルのパラメータを設定し、モデルの性能評価、係数の可視化、ROC曲線の表示などを行うことができます。

## 入力 (Inputs)

*   **データ (Data)**: `Orange.data.Table`
    *   分析対象のデータセット。
    *   **仕様**:
        *   目的変数は二値（例: 0/1, Yes/Noなど、2つの値を持つ）の離散変数である必要があります。
        *   特徴量には連続変数および離散変数が使用可能です。離散特徴量は内部でワンホットエンコーディング (`drop_first=True` オプション付き) されます。
        *   欠損値 (NaN) を含む行は、特徴量または目的変数のいずれかに欠損値があれば、分析前に除外されます。
    *   **例**: `heart_disease` データセット、タイタニック号の生存予測データなど。

### 入力データの例

以下は、このウィジェットで使用できる入力データの概念的な例です（例: ローン審査データ）。

| loan\_amnt (特徴量) | term (特徴量) | int\_rate (特徴量) | grade (特徴量) | installment (メタ) | loan\_status (目的変数) |
| :------------------ | :------------ | :----------------- | :------------- | :----------------- | :---------------------- |
| 10000               | 36 months     | 0.07               | B              | 308.91             | Default                 |
| 2500                | 60 months     | 0.15               | D              | 59.83              | Fully Paid              |
| 5000                | 36 months     | 0.11               | A              | 163.49             | Default                 |
| ...                 | ...           | ...                | ...            | ...                | ...                     |

*   `loan_amnt`, `int_rate`: 連続特徴量
*   `term`, `grade`: 離散特徴量（内部でワンホットエンコードされます。例えば `grade` がA, B, Cの場合、`grade_B`, `grade_C` のようなダミー変数が生成されます）
*   `installment`: メタ情報（モデル学習には使用されません）
*   `loan_status`: 二値の目的変数 (例: "Default" と "Fully Paid" の2つの値)

## 出力 (Outputs)

*   **係数 (Coefficients)**: `Orange.data.Table`
    *   モデルの各特徴量（および切片）に関する詳細情報を含むテーブル。
    *   列: `variable` (メタ, 特徴量名), `coefficient` (係数値), `p_value` (p値、正則化なしの場合のみ), `odds_ratio` (オッズ比), `importance` (重要度、係数の絶対値), `VIF` (分散拡大係数、切片の場合はNaN)。
*   **予測 (Predictions)**: `Orange.data.Table`
    *   元の入力データ（欠損値処理後）に、モデルによる予測クラス (`predicted_<target_name>`) と各クラスに属する予測確率 (例: `prob_ClassA`, `prob_ClassB`) が付加されたテーブル。

## 機能の説明

このウィジェットは、コントロールエリア（左パネル）でモデルの設定を行い、「適用」ボタンを押すことでメインエリア（右パネル）に分析結果を表示します。

### コントロールエリア (左パネル)

1.  **データ変数 (Data Variables)**
    *   **特徴量 (Features)**: モデルの学習に使用する説明変数をリストから選択（ドラッグ＆ドロップ可能）。
    *   **目的変数 (Target Variables)**: 分類の対象となる目的変数を1つ選択。二値の離散変数である必要があります。
    *   **メタ変数 (Meta Variables)**: モデル学習には使用しないが、出力データに含めたい変数をリストから選択。

2.  **モデルタイプ (Model Type)**
    *   **モデルタイプ選択**: 現在は「ロジスティック回帰 (Logistic Regression)」に固定されています。

3.  **正則化 (Regularization)**
    *   **正則化タイプ**:
        *   `None`: 正則化を行いません（内部で `statsmodels` ライブラリを使用）。この場合、p値などが計算されます。
        *   `L1 (Lasso)`: L1正則化。不要な特徴量の係数を0にする傾向があります（内部で `scikit-learn` を使用）。
        *   `L2 (Ridge)`: L2正則化。係数が大きくなることを防ぎます（内部で `scikit-learn` を使用）。
        *   `Elastic Net`: L1とL2の両方のペナルティを組み合わせた正則化（内部で `scikit-learn` を使用）。
    *   **正則化強度 (α)**: 正則化の強さを調整するスライダー（0.001 から 1.000）。正則化タイプが `None` 以外の場合に有効です。`scikit-learn` の `C` パラメータとしては `1/α` が使用されます。

4.  **高度なオプション (Advanced Options)**
    *   **切片を適合 (Fit intercept)**: モデルに切片（定数項）を含めるかどうかを指定します。
    *   **特徴量を正規化 (Normalize features)**: 学習前に特徴量を標準化（平均0、分散1にスケーリング）するかどうかを指定します。内部で `sklearn.preprocessing.StandardScaler` が使用されます。
    *   **クラス重み (Class weights)**: クラス間のサンプル数の不均衡に対処するためのオプション。
        *   `None`: 重み付けなし。
        *   `Balanced`: サンプル数が少ないクラスの重みを大きく調整します。
        *   このオプションは、正則化タイプが `None` 以外の場合（`scikit-learn` 使用時）に有効です。正則化タイプが `None` の場合、このオプションは無効化され、自動的に `None` に設定されます。

5.  **適用 (Apply)**
    *   現在の設定でモデルの再学習と結果の更新を行います。データが入力されていれば、ウィジェット起動時やデータ変更時に自動的に適用されることもあります。

### メインエリア (右パネル)

メインエリアはスクロール可能で、以下の分析結果が表示されます。

1.  **モデル性能 (Model Performance)**
    *   学習データに対するモデルの主要な評価指標が表示されます。
        *   `Accuracy` (正解率)
        *   `Precision` (適合率)
        *   `Recall` (再現率)
        *   `F1 Score` (F1値)
        *   `AUC` (ROC曲線下面積)
        *   `Log Loss` (対数損失)

2.  **混同行列 (Confusion Matrix)**
    *   実際のクラスとモデルによる予測クラスをクロス集計した表。各セルの値は該当するサンプル数と、全体に占める割合 (%) で表示されます。対角要素（正しく分類されたサンプル）は薄緑色、非対角要素（誤分類されたサンプル）は薄橙色で強調表示されます。

    |                 | 予測: Negative | 予測: Positive |
    | :-------------- | :------------- | :------------- |
    | **実際: Negative** | TN (件数, %)   | FP (件数, %)   |
    | **実際: Positive** | FN (件数, %)   | TP (件数, %)   |

3.  **係数と変数重要度 (Coefficients & Variable Importance)**
    *   モデルの各特徴量（および切片）に関する詳細な情報がテーブル形式で表示されます。
        *   `VARIABLE`: 特徴量名または切片。ワンホットエンコードされた変数は元の変数名と値で表示されます (例: `grade_B`)。
        *   `COEFFICIENT`: 学習された係数値。
        *   `P-Value`: 係数のp値（正則化タイプが `None` の場合のみ表示。0.05以下の場合は青文字、それより大きい場合は赤文字で表示）。
        *   `ODDS RATIO`: オッズ比 (`exp(coefficient)`)。
        *   `IMPORTANCE`: 変数重要度（係数の絶対値）。
        *   `VIF`: 分散拡大係数 (Variance Inflation Factor)。多重共線性の指標。一般的に5や10を超えると問題ありとされることがあります。切片の場合は `-` (または NaN) と表示。VIFが5以上10未満の場合はオレンジ文字、10以上の場合は赤文字で表示されます。

4.  **係数可視化 (Coefficient Visualization)**
    *   切片を除く各特徴量の係数値をバープロットで表示します。
    *   正の係数はオレンジ色、負の係数は青色のバーで示されます。
    *   バーの長さが係数の絶対的な大きさを表します。
    *   変数の数に応じてプロットの高さとバーの太さが動的に調整されます。これにより、多数の変数がある場合でも見やすさが維持されます。

5.  **ROC曲線 (ROC Curve)**
    *   モデルの識別能力を視覚的に評価するためのROC曲線が表示されます。
    *   横軸は偽陽性率 (False Positive Rate, FPR)、縦軸は真陽性率 (True Positive Rate, TPR) です。
    *   曲線が左上に近いほど、モデルの性能が良いことを示します。
    *   対角線（黒色の破線）はランダムな予測の場合を示します。
    *   プロットのタイトルにはAUC (Area Under the Curve) の値が小数点以下3桁まで表示されます (例: `ROC Curve (AUC = 0.709)`)。

## 使用例 (Example)

以下の例では、ローンデータセットを使用して `Binary Analysis` ウィジェットの基本的な使用法を示します。

![Binary Analysis ウィジェットの使用例](overall_widget_view.png)

1.  **データロード**: まず、`File` ウィジェットを使用して分析したいデータセット（例: ローン審査データ）をAllyeに読み込みます。このデータには、ローンの申請額 (`loan_amnt`)、期間 (`term`)、金利 (`int_rate`)、ローングレード (`grade`) などの特徴量と、ローンの結果 (`loan_status`) を示す目的変数が含まれているとします。

2.  **ウィジェット接続**: `File` ウィジェットの出力を `Binary Analysis` ウィジェットの `Data` 入力に接続します。

3.  **変数設定**:
    *   `Binary Analysis` ウィジェットを開き、左側のコントロールパネルで変数を設定します。
    *   **特徴量 (Features)**: `loan_amnt`、`term`、`int_rate` などを特徴量リストに移動します。画像では、カテゴリカル変数 `grade` から生成されたダミー変数 (`grade_B` など) も特徴量として使用されています。
    *   **目的変数 (Target Variables)**: `loan_status` を目的変数リストに設定します。これは二値（例: 0 = 完済, 1 = 焦げ付き）である必要があります。
    *   **メタ変数 (Meta Variables)**: `installment` や `sub_grade` のように、分析には直接使用しないが付随情報として保持したい変数をメタ変数リストに移動します。

4.  **モデルパラメータ設定**:
    *   **モデルタイプ**: `Logistic Regression` が選択されています。
    *   **正則化 (Regularization)**: 例では `L1 (Lasso)` が選択され、正則化強度 (α) が `0.218` に設定されています。これにより、一部の特徴量の係数が0になり、変数選択が行われる可能性があります。
    *   **高度なオプション**: `Fit intercept`（切片を適合）と `Normalize features`（特徴量を正規化）がチェックされています。`L1 (Lasso)` を使用しているため、クラス重みは `None` または `Balanced` を選択可能で、例では `Balanced` が選択されている可能性があります（UIからは直接読み取れませんが、L1使用時は有効なオプションです）。

5.  **分析実行と結果確認**:
    *   「Apply」ボタンをクリックする（または自動適用される）と、ロジスティック回帰モデルが学習され、右側のメインパネルに結果が表示されます。
    *   **Model Performance**: 正解率 (Accuracy: 0.909)、F1スコア (F1 Score: 0.952)、AUC (0.709) などの指標を確認できます。
    *   **Confusion Matrix**: 実際のクラスと予測クラスのクロス集計により、モデルの誤分類の傾向を把握できます。
    *   **Coefficients & Variable Importance**: 各特徴量の係数値、P値（L1正則化のため表示なし）、オッズ比、重要度、そしてVIF値がテーブルで表示されます。`const` は切片を示します。
    *   **Coefficient Visualization**: 係数の大きさと方向（正または負）が視覚的にバープロットで示されます。
    *   **ROC Curve**: ROC曲線とAUC値 (AUC = 0.709) により、モデルの識別能力を評価できます。

6.  **出力の活用**:
    *   `Binary Analysis` ウィジェットの **Predictions** 出力を `Data Table` ウィジェットに接続すると、元のデータに予測クラスと各クラスへの所属確率が付加された結果を確認できます。
    *   **Coefficients** 出力を別の `Data Table` に接続すると、係数の詳細（VIF値を含む）をより詳しく分析できます。

```mermaid
graph LR
    A[File (ローンデータ)] -- Data --> B(Binary Analysis)
    B -- Predictions --> C[Data Table (予測結果)]
    B -- Coefficients --> D[Data Table (係数詳細)]
```

## 詳細なロジック

1.  **データ前処理 (`preprocess_data` メソッド)**:
    *   ウィジェットに入力された `Orange.data.Table` は、内部的に pandas DataFrame に変換されます。
    *   ユーザーがコントロールエリアで選択した特徴量、目的変数、メタ変数に基づいてデータが再構成されます。
    *   **カテゴリカル変数のエンコーディング**: 選択された特徴量のうち、離散変数（カテゴリカル変数）はワンホットエンコーディング (`pd.get_dummies` を使用し、`drop_first=True` オプションで多重共線性を回避）されます。エンコーディング後の特徴量名 (`self.feature_names`) と、元の変数名と生成されたダミー変数名のマッピング情報 (`self.dummy_feature_mapping`) が保持されます。
    *   **欠損値処理**: 特徴量 (X) または目的変数 (y) に一つでも欠損値 (NaN) を含む行は、分析対象から除外されます。
    *   最終的に、数値化された特徴量行列 `self.X` と目的変数ベクトル `self.y` がモデル学習用に準備されます。

2.  **モデル構築 (`build_model` メソッド)**:
    *   `glm_utils.logistic_model.LogisticRegressionModel` クラスのインスタンスが、ユーザー設定（切片、正規化、正則化タイプ、正則化強度、クラス重み）に基づいて作成されます。
    *   **VIF計算**: この段階で、`LogisticRegressionModel` の `fit` メソッド内で、ワンホットエンコード後かつスケーリング前の特徴量データ (`X_df_for_vif`) を用いてVIF (分散拡大係数) が計算され、`self.vif_series` に格納されます。これは、元の特徴量間の多重共線性を評価するためです。
    *   **正則化なし (`regularization="None"`) の場合**:
        *   内部で `statsmodels.Logit` が使用され、モデルが学習されます。
        *   この場合、係数のp値や標準誤差などの統計的情報が計算されます。
        *   「クラス重み (`class_weight`)」オプションは無効です。
    *   **正則化あり (L1, L2, Elastic Net) の場合**:
        *   内部で `sklearn.linear_model.LogisticRegression` が使用されます。
        *   正則化強度 `alpha` は、`scikit-learn` の `C` パラメータ (`C = 1/alpha`) に変換されてモデルに渡されます。
        *   「クラス重み (`class_weight`)」オプションが有効になります。
    *   「特徴量を正規化 (`normalize_features=True`)」が選択されている場合、モデル学習前に `sklearn.preprocessing.StandardScaler` を用いて特徴量が標準化されます。このスケーラーは学習データで `fit_transform` され、以降の予測時には `transform` のみが行われます。
    *   準備された `self.X` と `self.y`、およびエンコーディング後の特徴量名リスト (`self.feature_names` または `model.input_feature_names_for_vif`) を用いてモデルが学習されます (`model.fit()`)。

3.  **モデル評価 (`evaluate_model` メソッド)**:
    *   学習済みモデル (`self.model`) を使用して、学習データ (`self.X`, `self.y`) に対する予測クラスラベル (`y_pred`) と各クラスへの所属確率 (`y_proba`) を取得します。データは必要に応じて `_prepare_data` (スケーリングなど) を通されます。
    *   `sklearn.metrics` モジュール内の関数群を用いて、以下の評価指標が計算されます:
        *   `accuracy_score` (正解率)
        *   `precision_score` (適合率, `zero_division=0` 設定)
        *   `recall_score` (再現率, `zero_division=0` 設定)
        *   `f1_score` (F1値, `zero_division=0` 設定)
        *   `roc_auc_score` (AUC)
        *   `log_loss` (対数損失)
        *   `confusion_matrix` (混同行列)
        *   `roc_curve` (ROC曲線描画用の偽陽性率 FPR と真陽性率 TPR)
    *   これらの評価結果は辞書型オブジェクト `self.evaluation_results` に格納されます。

4.  **結果表示 (`display_results` メソッド)**:
    *   `self.evaluation_results` に格納された情報やモデルから取得した情報（係数、VIFなど）を、メインエリアの各GUIコンポーネント（ラベル、テーブル、プロット）に整形して表示します。
    *   係数テーブルには、切片を含む全ての係数、p値（`statsmodels` 使用時のみ）、オッズ比（`exp(coefficient)`）、重要度（係数の絶対値）、VIF値が表示されます。VIF値はモデルの `get_vif_values()` メソッドから取得され、係数テーブルの各変数名と対応付けられます。
    *   係数可視化プロットには、切片を除いた特徴量の係数がバープロットで表示されます。
    *   ROC曲線プロットには、FPR、TPR、および計算されたAUC値が表示されます。

5.  **出力データの生成**:
    *   **係数 (`create_coefficients_table` メソッド)**: モデルから取得した係数名、係数値、p値、オッズ比、重要度、VIF値を元に、新しい `Orange.data.Table` を生成して出力します。ドメインは、属性として `ContinuousVariable("coefficient")`, `ContinuousVariable("p_value")`, `ContinuousVariable("odds_ratio")`, `ContinuousVariable("importance")`, `ContinuousVariable("VIF")` を、メタ情報として `StringVariable("variable")` を持ちます。
    *   **予測 (`create_predictions_table` メソッド)**: 前処理後の入力データ (`self.preprocessed_data`) をベースに、モデルによる予測クラスラベル (例: `predicted_target`) と各クラスへの予測確率 (例: `prob_0`, `prob_1`) を新しい列として追加した `Orange.data.Table` を生成して出力します。