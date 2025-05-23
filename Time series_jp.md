# Time Series Plot (時系列プロット)

時系列データを可視化し、トレンド、季節性、予測などの分析を行うウィジェットです。

## 入力 (Inputs)

-   `Data`: 時系列データを含むOrangeのTableオブジェクト。
    -   **仕様**:
        -   少なくとも1つの時間変数が必要です。時間変数はOrangeの `TimeVariable` 型が推奨されますが、 `ContinuousVariable` (数値型、内部的にタイムスタンプとして扱われる想定) や `StringVariable` (文字列型で `%Y-%m-%d` や `%Y-%m-%d %H:%M:%S` のような一般的な日付・日時フォーマット) も時間として解釈しようと試みます。
        -   少なくとも1つの数値変数 (`ContinuousVariable`) が値として必要です。
    -   **入力データ例**:
        以下は、日別の売上と在庫データを含むテーブルの例です。

        | 日付 (TimeVariable) | 売上 (ContinuousVariable) | 在庫 (ContinuousVariable) |
        | :------------------ | :------------------------ | :------------------------ |
        | 2023-01-01          | 120.5                     | 300                       |
        | 2023-01-02          | 122.3                     | 295                       |
        | 2023-01-03          | 118.0                     | 310                       |
        | ...                 | ...                       | ...                       |

## 出力 (Outputs)

-   `Selected Data`: グラフ上でドラッグ操作により選択されたデータポイント。選択された範囲のデータがOrangeのTableオブジェクトとして出力されます。
-   `Forecast Data`: Prophetによる予測結果を含むTableオブジェクト。元のデータ列に加えて、各予測対象変数に対して以下の列が追加されます (存在する場合)。また、予測時点のタイムスタンプと可読な日時も含まれます。
    -   `time`: 元のデータと予測データのタイムスタンプ (ミリ秒単位)。
    -   `time_readable`: `time` 列を人間が読める形式の日時 (`YYYY-MM-DD HH:MM:SS`) に変換したもの。
    -   `[元の変数名]`: 元の時系列データの値。
    -   `[元の変数名]_forecast`: Prophetによる予測値 (yhat)。
    -   `[元の変数名]_forecast_lower`: 予測値の信頼区間の下限値 (yhat_lower)。
    -   `[元の変数名]_forecast_upper`: 予測値の信頼区間の上限値 (yhat_upper)。

## 機能の説明

Time Series Plotウィジェットは、コントロールエリアとグラフ表示エリアの2つの主要部分で構成されます。

![sqlexec_overview](./imgs/ts_overview.png)



### コントロールエリア

コントロールエリアでは、データの指定、表示方法、分析ツールの設定を行います。

![ts_ctrl1](./imgs/ts_ctrl1.png)
![ts_ctrl2](./imgs/ts_ctrl2.png)
![ts_ctrl3](./imgs/ts_ctrl3.png)

#### 1. データ設定 (Data Settings)

-   **時間カラム (Time Column)**: プロットの時間軸として使用する列を選択します。ドロップダウンリストからデータセット内の適切な時間変数を選択してください。
-   **値カラム (Value Column)**: プロットする数値データの列を選択します。リストから1つまたは複数の数値変数を選択できます（複数選択可）。

#### 2. 表示設定 (Display Settings)

-   **グラフタイプ (Graph Type)**:
    -   `折れ線グラフ (Line Chart)`: 時系列データを線で結んで表示します（デフォルト）。
    -   `散布図 (Scatter Plot)`: 時系列データを個々の点として表示します。
-   **集計期間 (Aggregation Period)**: データの集計単位を選択します。
    -   選択肢: `秒 (Second)`, `分 (Minute)`, `時 (Hour)`, `日次 (Daily)` (デフォルト), `週次 (Weekly)`, `月次 (Monthly)`, `四半期 (Quarterly)`, `年次 (Yearly)`。
    -   データは選択された期間ごとに平均値で集計されて表示されます。
-   **ツールチップとグリッド線を表示 (Show Tooltips and Grid Lines)**:
    -   チェックを入れると、グラフ上のデータポイントにマウスオーバーした際に詳細情報（日付、値など）が表示され、グラフにグリッド線が表示されます（デフォルトでオン）。
-   **スクロールでズーム (Zoom with Scroll)**:
    -   チェックを入れると、グラフ上でマウスホイールを操作することでズームイン・ズームアウトが可能になります（デフォルトでオフ）。
-   **選択を有効化 (Enable Selection)**:
    -   チェックを入れると、グラフ上でマウスをドラッグしてデータ範囲を選択し、そのデータを `Selected Data` 出力から送信できます（デフォルトでオン）。
-   **グラフスタイル (Graph Style)**:
    -   **線の太さ (Line Width)**: 折れ線グラフの線の太さをスライダーで調整します（1〜10、デフォルト2）。
    -   **ポイントサイズ (Point Size)**: 散布図の点の大きさをスライダーで調整します（2〜20、デフォルト8）。



#### 3. 分析ツール (Analysis Tools)

-   **トレンドライン表示 (Show Trendline)**:
    -   チェックを入れると、選択された各値カラムに対して線形回帰によるトレンドラインが破線で表示されます。
-   **移動平均 (Moving Average)**:
    -   チェックを入れると、移動平均線が表示されます。
    -   **ウィンドウサイズ**: 移動平均を計算する期間（ウィンドウ）を数値で指定します（2〜30、デフォルト7）。
    -   **単位**: ウィンドウサイズの単位（例: `days`）は「集計期間」に連動して表示されます。
-   **季節性分解 (Seasonality Decomposition)**:
    -   チェックを入れると、時系列データをトレンド成分、季節性成分、残差成分に分解し、それぞれを個別のグラフとしてメインプロットの下に表示します。
    -   **周期 (period)**: 季節性の周期を数値で指定します（2〜365、デフォルト7）。例えば、日次データで週周期を見る場合は7を指定します。単位は「集計期間」のステップ数です。
-   **Prophet予測 (Prophet Forecasting)**: Facebookが開発したProphetライブラリを用いて時系列予測を行います。
    -   チェックを入れると、予測関連の設定項目が有効になります。
    -   **トレーニング期間 (Training period)**:
        -   `Auto (use all data)`: 入力データの全期間をモデルの学習に使用します（デフォルト）。
        -   `Custom`: スライダーで学習に使用するデータの開始日と終了日を指定します。
    -   **予測期間 (Forecast horizon (steps))**: 何ステップ先まで予測するかを数値で指定します（1〜1825 (5年分相当)、デフォルト30）。ステップの単位は「集計期間」で選択したものになります（例: 集計期間が日次なら30日先、週次なら30週先）。
    -   **信頼区間表示 (Show confidence interval)**:
        -   チェックを入れると、予測値の信頼区間がグラフ上に表示されます（デフォルトでオン）。
        -   ドロップダウンリストから信頼度を選択します (`80%`, `90%`, `95%` (デフォルト), `99%`)。
    -   **変化点表示 (Show changepoints)**:
        -   チェックを入れると、Prophetが検出したトレンドの変化点がグラフ上に表示されます（デフォルトでオン）。
        -   **Prior scale**: 変化点の検出感度を調整します（0.001〜0.5、デフォルト0.05）。値が大きいほど多くの変化点を検出しやすくなります。
    -   **季節性の詳細設定 (Seasonality Details)**:
        -   **Yearly Seasonality**: 年次の季節性を考慮するかどうか (`True`, `False`, `Auto` (デフォルト))。時間/分/秒単位のデータでは `False` または `Auto` が推奨されることがあります。
        -   **Weekly Seasonality**: 週次の季節性を考慮するかどうか (`True`, `False`, `Auto` (デフォルト))。非常に短期（例: 数時間の秒単位データ）のデータでは `False` または `Auto` が適切な場合があります。
        -   **Daily Seasonality**: 日次の季節性を考慮するかどうか (`True`, `False`, `Auto` (デフォルト))。複数日にわたるデータ、特に時間/分単位のデータで関連します。
    -   **カスタム季節性 (Custom Seasonalities)**:
        -   `Add Custom Seasonality` ボタンで独自の季節性パターンを追加できます。
            -   **Name**: 季節性の名前（例: `monthly`）。
            -   **Period**: 季節性の周期（ステップ数）。単位は「集計期間」に依存します。
            -   **Fourier Order**: 季節性をモデル化するためのフーリエ級数の次数。
            -   **Prior Scale**: 季節性の強さの事前分布のスケール（デフォルト10.0）。
            -   **Mode**: 季節性の種類 (`additive` (デフォルト) または `multiplicative`)。
    -   **外れ値と成長モデル (Outliers and Growth Model)**:
        -   **Enable Outlier Capping (Logistic Growth only)**: ロジスティック成長モデル使用時に、指定した上限(Cap)・下限(Floor)を超える値を丸める外れ値処理を有効にします（デフォルトでオフ）。
        -   **Growth Model**: トレンドの成長モデルを選択します (`linear` (デフォルト) または `logistic`)。
        -   **Cap (Upper limit for logistic growth)**: ロジスティック成長モデルの上限値。`Enable Outlier Capping`がオンかつ`Growth Model`が`logistic`の場合に有効。
        -   **Floor (Lower limit for logistic growth, optional)**: ロジスティック成長モデルの下限値。`Enable Outlier Capping`がオンかつ`Growth Model`が`logistic`の場合に有効。
    -   **予測実行 (Run Forecast)**:
        -   上記の設定に基づいて予測モデルを学習し、予測結果をグラフに表示および `Forecast Data` 出力から送信します。

### グラフ表示エリア




-   **メインプロット**:
    -   選択された時系列データ、トレンドライン、移動平均線、Prophetによる予測結果などが表示されます。
    -   複数の値カラムを選択した場合、異なる色でプロットされます。
    -   凡例が右上に表示され、各系列の識別が可能です。
-   **季節性分解プロット** (「季節性分解」が有効な場合のみ表示):
    -   メインプロットの下に、トレンド (Trend)、季節性 (Seasonality)、残差 (Residual) の各成分が個別のグラフで表示されます。
    -   これにより、データの構造を詳細に把握できます。

    ![ts_decom](./imgs/ts_decom.png)

-   **ツールチップ**:
    -   「ツールチップとグリッド線を表示」が有効な場合、グラフ上のデータポイントにマウスカーソルを合わせると、そのポイントの日付（または時間）と値、表示されている場合は移動平均値や予測値などが表示されます。同時に、マウス位置に追従する赤い縦の点線が表示されます。
-   **インタラクション**:
    -   ズーム: 「スクロールでズーム」が有効な場合、マウスホイールでズーム操作ができます。無効な場合は、グラフ右クリックメニューからズーム操作を行います。
    -   パン: マウスの左ボタンドラッグでグラフを移動できます（「選択を有効化」が無効な場合、または選択モードでない場合）。
    -   データ選択: 「選択を有効化」が有効な場合、マウスの左ボタンドラッグで矩形範囲を選択し、範囲内のデータポイントを `Selected Data` として出力できます。選択されたポイントはグラフ上でハイライトされます。

## 使用例

![ts_flow](./imgs/ts_flow.png)

1.  **基本的な時系列データの可視化**:
    -   `File` ウィジェットで時系列データ (例: `air_quality.csv`) を読み込みます。
    -   `File` ウィジェットの出力を `Time Series Plot` ウィジェットの `Data` 入力に接続します。
    -   `Time Series Plot` ウィジェットを開き、「データ設定」で適切な「時間カラム」と1つ以上の「値カラム」を選択します。
    -   「表示設定」で「グラフタイプ」や「集計期間」を調整してデータを表示します。

2.  **季節性の分析**:
    -   上記1の状態で、「分析ツール」の「季節性分解」にチェックを入れます。
    -   データの特性に合わせて「周期」を設定します (例: 日次データで週周期なら7、月次データで年周期なら12)。
    -   メインプロットの下にトレンド、季節性、残差のグラフが表示され、データの周期的変動を確認できます。
        (画像参照: `../../_images/TimeSeriesPlot-Seasonality.png` - 季節性分解表示のイメージ)

3.  **将来予測**:
    -   上記1の状態で、「分析ツール」の「Prophet予測」にチェックを入れます。
    -   「予測期間」や「信頼区間」などのパラメータを設定します。詳細設定で季節性のパターンや成長モデルも調整できます。
    -   「予測実行」ボタンをクリックすると、学習データに基づいて未来の予測値がグラフに描画され、予測結果が `Forecast Data` ポートから出力されます。
    -   この `Forecast Data` を別の `Data Table` ウィジェットに接続して、具体的な数値を確認することもできます。


## 詳細なロジック

-   **データ処理**:
    -   入力されたOrange Tableから、指定された時間変数と値変数を抽出します (`extract_time_series_data`)。時間変数は内部的にミリ秒単位のタイムスタンプとして扱われます。
    -   選択された「集計期間」に基づき、データを集計します (`aggregate_time_series_data`)。集計処理にはpandasライブラリが内部で使用されます。
-   **季節性分解**:
    -   `statsmodels.tsa.seasonal.seasonal_decompose` 関数を利用して、時系列データをトレンド、季節性、残差に加法的に分解します (`decompose_time_series`)。
-   **Prophet予測**:
    -   Facebookの `prophet` ライブラリを使用して時系列予測モデルを構築し、予測を行います (`run_prophet_forecast`)。ユーザーが設定したトレーニング期間、予測期間、信頼区間、変化点感度、季節性の詳細、成長モデル、カスタム季節性などのパラメータがモデルに渡されます。
-   **グラフ描画**:
    -   グラフの描画には `pyqtgraph` ライブラリが使用されています。
    -   時間軸には `DateAxisItem` を使用し、日付や時刻を適切に表示します。
    -   ツールチップ、凡例、選択矩形などのインタラクティブな要素も `pyqtgraph` の機能を用いて実装されています。
    -   季節性分解の結果は、メインプロットの下に動的に追加される複数のサブプロットとして表示されます。
