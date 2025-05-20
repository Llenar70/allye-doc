# Node Builder

Node Builderは、AIアシスタント「Allye」と対話することで、Allyeのワークフローを自動的に生成および実行するウィジェットです。

| アイコン                                         |
| :---------------------------------------------: |
| ![](_images/ally_face_centered_icon_48x48.png) |

## 入力

- **Data**: (Orange.data.Table, オプショナル)
  - 分析対象のデータセット。接続されていない場合、Allyeはファイル読み込みウィジェットを含むワークフローを提案することがあります。

## 出力

- **out_data**: (Orange.data.Table)
  - 入力された `Data` をそのまま出力します。このウィジェット自体はデータを加工しません。

## 機能

Node Builderウィジェットは、主に以下の機能を提供します。

1.  **AIアシスタントとの対話**:
    *   チャットインターフェースを通じて、自然言語で分析タスクを指示できます。
    *   Allyeが指示を解釈し、応答します。
2.  **ワークフロー自動生成**:
    *   「Build Nodes」ボタン、またはチャットでの指示により、Allyeが分析目的に合ったAllyeのワークフローを提案します。
    *   提案されたワークフローは、自動的にキャンバス上にノードとして配置・接続されます。
3.  **データ連携**:
    *   入力データセットのカラム情報（変数名、型、役割）や、少量のサンプルデータをAllyeに送信することで、よりデータに適したワークフローの生成を支援します。
    *   これらの情報の送信は、ユーザーがコントロールパネルで制御できます。
4.  **変数管理**:
    *   コントロールパネルで、データセット内の変数を「特徴量(Features)」「ターゲット変数(Target Variables)」「メタ変数(Meta Variables)」に分類できます。この分類情報はAllyeに提供されます。
5.  **Pythonコード実行と検証**:
    *   Allyeが生成するPythonコード（特にNotebookウィジェット内で実行されるデータ処理コード）に対して、基本的な検証と自動修正を試みます。

## UI説明

Node Builderウィジェットのインターフェースは、主にチャットエリアとコントロールエリアで構成されます。

### メインエリア (チャット)

![](_images/node_builder_main_area_annotated.png) <!-- 注釈付きスクリーンショットを推奨 -->

1.  **ヘッダー**:
    *   **タイトル (A)**: 「Analytics Assistant」
    *   **ステータスラベル (B)**: Allyeの状態（例: Ready, Thinking..., Error occurred）を表示します。
2.  **チャットエリア (C)**:
    *   Allyeからのメッセージ（ウェルカムメッセージ、応答、システムログなど）と、ユーザーが送信したメッセージが表示されます。
    *   Allyeが生成したコードやワークフローの計画もここに表示されます。
3.  **入力エリア (D)**:
    *   **テキスト入力ボックス**: Allyeへの指示や質問をここに入力します。「Cmd+Enter」または「Ctrl+Enter」でも送信可能です。
    *   **送信ボタン (紙飛行機アイコン)**: 入力したメッセージをAllyeに送信します。

### コントロールエリア (サイドパネル)

![](_images/node_builder_control_area_annotated.png) <!-- 注釈付きスクリーンショットを推奨 -->

1.  **Data Variables (E)**:
    入力データセットの変数を管理します。変数をドラッグ＆ドロップして役割を変更できます。
    *   **Features**: 分析に使用する特徴量。
    *   **Target Variables**: 予測対象などのターゲット変数。
    *   **Meta Variables**: 分析には直接使用しないが、参照情報として保持するメタ変数。
2.  **Auto Generation (F)**:
    *   **Build Nodes ボタン**: 入力データと現在の会話コンテキストに基づいて、Allyeにワークフロー全体の自動生成を指示します。データが接続されていない場合は、データソースの作成から提案されることがあります。
3.  **Action Settings (G)**:
    Allyeに送信するデータ情報を制御します。
    *   **Send column and attribute information**: チェックすると、データセットのカラム名、型、役割（Features/Target/Meta）、カテゴリ変数の場合はユニークな値の数などのヘッダー情報をAllyeに送信します。
    *   **Send sample data (5 rows)**: チェックすると、データセットからランダムに5行のサンプルデータをAllyeに送信します。**注意: 生データがLLMと共有されます。**

## 使用例

### 例1: チャットによる指示でのノード生成

1.  FileウィジェットなどからNode Builderウィジェットにデータセット (例: `iris.tab`) を接続します。
2.  Node Builderのチャット入力ボックスに「petal lengthとpetal widthで散布図を作成して」と入力し、送信します。
3.  Allyeが指示を解釈し、Scatter Plotウィジェットを生成して入力データに接続し、指定された変数を設定します。

![](_images/node_builder_example1.png)

### 例2: 「Build Nodes」ボタンによる自動ワークフロー生成

1.  FileウィジェットなどからNode Builderウィジェットにデータセット (例: `housing.tab`) を接続します。
2.  コントロールエリアの「Build Nodes」ボタンをクリックします。
3.  Allyeがデータセットの内容（カラム情報やサンプルデータ、変数の役割設定に基づく）と一般的な分析フローを考慮して、データの前処理、可視化、モデル構築などを含むワークフローを提案・生成します。

![](_images/node_builder_example2.png)

### 例3: Notebookウィジェット内のPythonコード生成

1.  FileウィジェットからNode Builderにデータを接続します。
2.  チャットで「ageが30以上のデータのみを抽出するPythonコードをNotebookで生成して」と指示します。
3.  AllyeはNotebookウィジェットを生成し、その中に以下のようなPythonコードを記述します。
    ```python
    # dfは入力データフレームとします
    df_filtered = df[df['age'] >= 30]
    ```
    Node Builderは生成されたコードを解釈し、Notebookウィジェットが `df` を出力するように設定します。

## 詳細なロジック

Node Builderウィジェットは、バックグラウンドで複数のコンポーネントを連携させて動作します。

1.  **データ入力と前処理 (`SimpleAgentAllyWidget.set_data`, `DataProcessingWorker`)**:
    *   ウィジェットにデータが入力されると、変数リスト（Features, Target, Meta）がUI上で更新されます。
    *   ユーザーがAllyeにメッセージを送信、または「Build Nodes」を実行すると、`DataProcessingWorker` が非同期で起動します。
    *   `DataProcessingWorker` は、UIの「Action Settings」での設定（`send_column_info`, `send_sample_data`）に応じて、入力データから以下の情報を準備します。
        *   **カラム情報**: 変数名、型（数値、カテゴリ、文字列、時間）、役割（特徴量、ターゲット、メタ）、カテゴリ変数の場合はユニーク値の数や一部の値、時間変数の場合はフォーマット例などをMarkdownテーブル形式で生成します。
        *   **サンプルデータ**: 指定された行数のサンプルデータをMarkdownテーブル形式で生成します。
2.  **AIとの対話とプロンプト生成 (`GeminiAPIWorker`)**:
    *   準備されたデータ情報とユーザーの入力（または自動生成指示）は、`GeminiAPIWorker` に非同期で渡されます。
    *   `GeminiAPIWorker` は、Google Geminiの大規模言語モデル（LLM）と通信します。
    *   **通常チャットモード**:
        *   会話履歴、ユーザーの現在の入力、およびデータ情報（カラム情報テーブル、サンプルデータテーブル）を含むプロンプトを構築します。
        *   システムプロンプトには、Allyeの役割（Orange3ベースのツール「Allye」のアシスタントであることなど）が定義されています。
        *   LLMからの応答をチャットエリアに表示します。
    *   **ワークフロー自動生成モード (`on_run`メソッドから起動)**:
        1.  まず、現在の会話履歴とユーザーの意図（もしあれば）に基づき、LLMに分析目的の**要約**を生成させます。（この際、データコンテキストは通常、要約自体には提供されません。）
        2.  次に、その要約、データ情報（カラム情報テーブル、サンプルデータテーブル）、およびワークフロー生成に特化したシステムプロンプト（利用可能なノード作成関数群の説明、生成すべきPythonコードの形式指定などを含む）をLLMに送信します。
        3.  LLMは、指定された形式（通常、単一のPythonコードブロック）でワークフローを構築するためのPythonコードを返します。
3.  **Pythonコードの検証と修正 (`_validate_and_correct_code_snippet`, `PythonCodeExecutorTool`)**:
    *   LLMがワークフロー生成の一環としてPythonコードスニペット（特に `configure_notebook` 関数呼び出し内の `generated_python_code`パラメータに渡される文字列としてのコード）を生成した場合、そのコードの検証と修正が試みられます。
    *   `PythonCodeExecutorTool` はLangChainのツールとして定義されており、受け取ったPythonコードスニペットを安全な環境で（pandas DataFrame `df` が利用可能な状態で）実行しようと試みます。
        *   この際、`matplotlib` のバックエンドは非対話的な 'Agg' に一時的に変更され、プロット生成によるGUIハングを防ぎます。
        *   `send_data_to_next_widget` のような、実際のOrange環境でのみ意味を持つ関数呼び出しは、この検証ステップでは無視（コードから除去）されます。
    *   コード実行でエラーが発生した場合、エラーメッセージと共に元のコードを再度LLM（LangChain経由で別のLLMChainを使用し、コード修正専用のプロンプトで）に渡し、修正を依頼します。このプロセスは最大3回繰り返されることがあります。
    *   検証・修正されたコード（または修正に失敗した場合は元のエラーを伴うコード）が、最終的なワークフロー生成コードに組み込まれます。
4.  **ワークフローコードの実行とノード配置 (`_process_workflow_response`, `exec()`, `arrange_nodes_by_connections`)**:
    *   LLMから返された（または検証・修正された）Pythonコード全体が `_process_workflow_response` メソッドで処理されます。
    *   このコードは `exec()` 関数を介して実行されます。実行時のグローバルスコープには、ノード作成 (`create_widget_node`) や各種ウィジェット設定関数 (`configure_scatterplot`, `configure_file` など）、現在のOrange Canvasのスキーマ (`scheme`)、Node Builderウィジェット自身 (`self`)、Node Builderウィジェットのノード (`source_node`) などが含まれます。
    *   コード内でこれらの関数が呼び出されることで、キャンバス上に新しいウィジェットが作成され、設定され、接続されます。
    *   ワークフローの全てのノードが配置された後、`arrange_nodes_by_connections` 関数が呼び出され、ノード間の接続関係に基づいてノードの位置を自動的に整列します。
5.  **設定とAPIキー**:
    *   Google Gemini APIキーは、ユーザーのホームディレクトリ以下の `~/.allye_secrets/config_allye.yaml` ファイルに保存されている必要があります。このファイルが存在しないか、APIキーが設定されていない場合、ウィジェットはチャットエリアに警告を表示し、API通信機能は利用できません。

## 入力データの仕様と例

Node Builderウィジェットは、標準的なOrange Table (`Orange.data.Table`) を入力として受け付けます。これには、数値特徴量、カテゴリ特徴量、ターゲット変数（クラス変数）、メタデータが含まれます。

**入力データ例 (Pythonコード)**:
```python
from Orange.data import Table, Domain, ContinuousVariable, DiscreteVariable

# 例1: irisデータセットの読み込み (Orangeに付属の .tab ファイルから)
# OrangeのFileウィジェットで "iris.tab" を読み込み、その出力をNode Builderに接続します。
# または、Python Scriptウィジェット内で以下のようにしてNode Builderに送ることも可能です。
# iris_data = Table("iris")
# self.send("Data", iris_data) # Python Scriptウィジェットから出力する場合

# 例2: 手動でデータテーブルを作成 (Python Scriptウィジェット内で)
features = [ContinuousVariable("age"), DiscreteVariable("gender", values=["Male", "Female"])]
class_var = DiscreteVariable("outcome", values=["Positive", "Negative"])
domain = Domain(features, class_var)

data_values = [
    [25, "Male", "Positive"],
    [30, "Female", "Negative"],
    [22, "Female", "Positive"]
]
custom_data = Table.from_list(domain, data_values)
# この custom_data をPython Scriptウィジェットの出力に設定し、Node Builderに接続できます。
```
データが入力されると、Node Builderは自動的に変数の型（連続値、離散値など）を認識し、コントロールパネルの「Data Variables」セクションに表示します。ユーザーはここで変数の役割（特徴量、ターゲット、メタ）をドラッグ＆ドロップで調整できます。これらの情報は、Allyeがより適切な分析ワークフローを提案するのに役立ちます。