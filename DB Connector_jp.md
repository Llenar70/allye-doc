# DB Connector

## 概要
DB Connectorウィジェットは、様々な種類のデータベースへの接続を確立し、その接続情報を管理するためのインターフェースを提供します。SQLAlchemyライブラリを利用して、PostgreSQL, MySQL, SQL Server, Redshift, BigQueryといった主要なデータベースに対応しています。保存された接続情報を簡単に再利用したり、新しい接続を素早く設定したりすることができます。

## 入力
このウィジェットには入力データはありません。

## 出力
*   **Connection (`DBConnection`)**: 接続に成功した場合に出力される接続情報オブジェクト。
    *   `name` (str): ユーザーが定義した接続名。
    *   `db_type` (str): 接続されたデータベースの種類 (例: "PostgreSQL", "BigQuery")。
    *   `engine` (SQLAlchemy `Engine`): データベース操作に使用できるSQLAlchemy Engineオブジェクト。このEngineは、後続のウィジェット（例: SQL Executorウィジェット）でクエリ実行などに利用されます。
    *   `last_connected` (str, Optional): 最終接続日時 (ISO 8601フォーマット文字列)。

## 機能の説明
DB Connectorウィジェットは、ユーザーがGUIを通じてデータベース接続を容易に設定・管理できるように設計されています。

![ DB connector ウィジェット全体像](./imgs/DB_overview.png)
*図1: Binary Analysisウィジェットの全体像。左に設定エリア、右に分析結果が表示される。*


主な機能は以下の通りです:
*   **多様なデータベースへの接続**: PostgreSQL, MySQL, SQL Server, Redshift, Google BigQueryといった複数のデータベースシステムに対応しています。
*   **接続情報の永続化**: 設定した接続情報は、ローカルのJSONファイル (`~/.allye_secrets/connections.json`) に安全に保存され、ウィジェット起動時に自動的に読み込まれるため、再利用が容易です。
*   **直感的でインタラクティブなUI**:
    *   保存済みの接続は、DBタイプを示すアイコンと共にリスト表示されます。
    *   検索ボックスを使用して、多数の保存済み接続の中から目的のものを素早く見つけることができます。
    *   選択したDBタイプに応じて、必要な入力フィールドが動的に表示されます。
    *   パスワード入力フィールドには、入力内容の表示/非表示を切り替えるトグルボタンが付いています。
*   **接続テスト機能**:
    *   `Connect` ボタンで手動で接続テストを実行できます。
    *   「Auto-test connection before saving」オプションを有効にすると、接続情報を保存する前に自動で接続テストが行われます。
*   **リアルタイムの接続ステータス表示**: 接続試行中、接続成功、未接続、接続エラーといった現在の状態を、色とテキストで明確に表示します。接続成功時には応答時間も表示されます。
*   **Google BigQuery認証サポート**:
    *   サービスアカウントキーファイル（JSON）のパスを指定する方法に加え、**`gcloud` コマンドラインインターフェース (CLI) を利用した認証機能**も提供します。
    *   <span style="color:red; font-weight:bold;">重要: BigQueryの `gcloud` 認証機能（`Run gcloud auth application-default login` ボタン）を利用するには、お使いのコンピュータに Google Cloud SDK (gcloud CLI) が事前にインストールされ、OSの環境変数PATHが正しく設定されている必要があります。</span>
*   **接続情報の管理**: 保存された接続情報の複製 (Clone) や削除がGUIから簡単に行えます。

## UIコンポーネントの説明

DB Connectorウィジェットの画面は、大きく分けてタイトルバー、左ペイン（保存された接続）、右ペイン（接続設定）で構成されています。

### 1. タイトルバー
*   ウィジェットの上部にあるオレンジ色のバーで、「DB Connector」というウィジェット名が表示されます。

### 2. 左ペイン: 保存された接続 (Saved Connections)
*   **ヘッダーラベル**: 「Saved Connections」と表示され、このセクションが保存済み接続リストであることを示します。
*   **検索ボックス (`Search connections...`)**: ここにテキストを入力すると、接続リストがリアルタイムでフィルタリングされ、名前に該当テキストを含む接続のみが表示されます。
*   **接続リスト**:
    *   保存済みの接続設定が一覧で表示されます。
    *   各項目には、データベースの種類を示すアイコン（例: BQ für BigQuery）、接続名が表示されます。
    *   項目にマウスカーソルを合わせると、接続名、DBタイプ、最終接続日時などの詳細情報がツールチップとして表示されます。
    *   リスト内の項目をクリックすると、その接続情報が右ペインのフォームに自動的に読み込まれます。

### 3. 右ペイン: 接続設定 (Connection Settings)
*   **ヘッダーラベル**: 「Connection Settings」と表示され、このセクションで接続情報を設定・編集することを示します。
*   **DB Type**:
    *   ドロップダウンリストから接続したいデータベースの種類を選択します (PostgreSQL, MySQL, SQL Server, Redshift, BigQuery)。
    *   選択すると、DBタイプに応じたアイコンが表示され、下部の接続詳細フォームの内容が切り替わります。
*   **接続詳細フォーム (DBタイプにより内容が変化)**:
    *   **汎用DB (PostgreSQL, MySQL, SQL Server, Redshift) 選択時**:
        *   `Project ID`: (このフォームでは表示されません)
        *   `Host`: データベースサーバーのホスト名またはIPアドレス。
        *   `Port`: データベースサーバーのポート番号 (DBタイプ選択時にデフォルト値が入力されます)。
        *   `Database`: 接続するデータベースの名前。
        *   `Username`: データベースへの接続に使用するユーザー名。
        *   `Password`: 上記ユーザー名に対応するパスワード。右端のボタンで入力内容の表示/非表示を切り替えられます。
        *   `Connection Name`: この接続設定を保存する際の識別名。左ペインのリストに表示されます。
    *   **BigQuery 選択時**:
        *   `Project ID`: Google CloudプロジェクトのID。
        *   `Credentials Path`: (オプション) Google CloudサービスアカウントキーのJSONファイルのフルパス。空の場合、`gcloud` のアプリケーションデフォルト認証情報 (ADC) が使用されます。
        *   `Authenticate`:
            *   `Run gcloud auth application-default login` ボタン: このボタンをクリックすると、`gcloud` CLIツールを利用してGoogle Cloudの認証プロセスが開始されます。通常、ブラウザが起動し、Googleアカウントでの認証を求められます。認証が成功すると、ADCが設定または更新されます。
            *   <span style="color:red; font-weight:bold;">繰り返しになりますが、この機能を使用するにはgcloud CLIがインストールされ、PATHが通っている必要があります。</span>
        *   `Connection Name`: この接続設定を保存する際の識別名。
*   **接続オプション (Connection Options)**:
    *   `Auto-test connection before saving` (チェックボックス): これをチェックすると、「Save / Update」ボタンで接続情報を保存する際に、自動的に接続テストが実行されます。テストに失敗した場合は保存されず、エラーが表示されます。
*   **ステータス表示エリア**:
    *   接続状態を示すインジケータ（色付きの丸）とテキストメッセージが表示されます。
        *   緑 (● Connected): 接続成功。応答時間も括弧内に表示されます (例: `(1213 ms)`)。
        *   灰色 (● Not Connected): 未接続または切断状態。
        *   赤 (● Connection Error): 接続試行に失敗。
        *   青 (● Testing Connection...): 現在、接続テストを実行中。
*   **操作ボタン群**:
    *   `Connect` (緑色): 現在フォームに入力されている情報でデータベースへの接続を試みます。成功すると、接続情報 (`DBConnection` オブジェクト) がウィジェットから出力され、ステータス表示が「Connected」に変わります。
    *   `Save / Update` (オレンジ色): 現在フォームに入力されている情報を、指定された「Connection Name」で接続リストに保存または更新します。
    *   `Clone` (薄いオレンジ色): 左ペインで選択されている接続情報を複製します。新しい接続名（通常は元の名前に "- Copy" が付加される）で新しいエントリが作成され、フォームに読み込まれます。
    *   `Delete` (灰色): 左ペインで選択されている接続情報をリストから削除します。確認ダイアログが表示されます。
*   **エラーメッセージ表示エリア**: `Connect` や `Save / Update` 時にエラーが発生した場合、その詳細がこのエリアに赤字で表示されます。

## 使用例

![DB connector ウィジェットの使用例](./imgs/DB_flow.png)

### 例1: 新しいPostgreSQLデータベース接続を作成し、接続する
1.  DB Connectorウィジェットを開きます。
2.  右ペインの「DB Type」ドロップダウンから「PostgreSQL」を選択します。
3.  PostgreSQLサーバーの「Host」（例: `localhost`）、「Port」（例: `5432`）、「Database」（例: `mydatabase`）、「Username」（例: `myuser`）、「Password」をそれぞれのフィールドに入力します。
4.  「Connection Name」に任意の名前（例: `My Local PostgreSQL`）を入力します。
5.  (オプション) 「Auto-test connection before saving」にチェックを入れます。
6.  `Save / Update` ボタンをクリックします。接続情報が左ペインのリストに追加されます。自動テストが有効な場合、ここで接続テストが実行されます。
7.  `Connect` ボタンをクリックします。フォーム下のステータス表示が緑色の「Connected」に変わり、応答時間が表示されれば接続成功です。この時点で、`DBConnection` オブジェクトがウィジェットの出力ポートから送信されます。

### 例2: Google BigQueryにgcloud CLI認証を使用して接続する
<span style="color:red; font-weight:bold;">前提: Google Cloud SDK (gcloud CLI) がお使いの環境にインストールされ、PATHが正しく設定されていること。</span>
1.  右ペインの「DB Type」ドロップダウンから「BigQuery」を選択します。
2.  「Project ID」にあなたのGoogle CloudプロジェクトID（例: `my-gcp-project-12345`）を入力します。
3.  「Credentials Path」フィールドは空のままにします（アプリケーションデフォルト認証情報を使用するため）。
4.  `Run gcloud auth application-default login` ボタンをクリックします。
    *   情報ダイアログが表示されたら内容を確認し、「Ok」をクリックします。
    *   システムのデフォルトブラウザが起動し、Googleアカウントでの認証を求めるページが表示されます。画面の指示に従って認証を完了してください。
    *   認証プロセスが完了すると、ウィジェット内に成功または失敗のメッセージが表示されます。
5.  「Connection Name」に任意の名前（例: `My BigQuery ADC`）を入力します。
6.  `Save / Update` ボタンをクリックして接続情報を保存します。
7.  `Connect` ボタンをクリックします。ステータス表示が「Connected」になれば接続成功です。

### 例3: 保存済みの接続設定を再利用する
1.  ウィジェットを開くと、以前保存した接続が左ペインのリストに表示されます。
2.  リストから利用したい接続名（例: `My Local PostgreSQL`）をクリックします。
3.  選択した接続の情報が右ペインのフォームに自動的に読み込まれます。
4.  `Connect` ボタンをクリックして、データベースに再接続します。

### 例4: 既存の接続設定を複製して一部変更する
1.  左ペインのリストから、複製元となる接続設定を選択します。
2.  右ペインにある `Clone` ボタンをクリックします。
3.  元の接続名に「- Copy」（または「- Copy 2」など、重複を避けた名前）が付加された新しい接続エントリが左ペインのリストに作成され、その情報が右ペインのフォームに読み込まれます。
4.  フォーム内の情報を必要に応じて変更します（例: 別のデータベース名を指定する、接続名をより分かりやすいものに変更する）。
5.  変更後、`Save / Update` ボタンをクリックして新しい接続設定を保存します。

## 詳細なロジック (内部動作)

### 接続情報の保存と読み込み
*   すべての接続設定は、ユーザーのホームディレクトリ内の `.allye_secrets` という隠しフォルダにある `connections.json` という名前のJSONファイルに保存されます。
    *   一般的なパス:
        *   Linux/macOS: `~/.allye_secrets/connections.json`
        *   Windows: `%USERPROFILE%\.allye_secrets\connections.json`
*   ファイルには、各接続設定がオブジェクトの配列として格納されます。各オブジェクトには、`name`, `db_type`, `host`, `port`, `database`, `username`, `password` (RDBの場合), `bq_project_id`, `bq_cred_path` (BigQueryの場合), `last_connected` などのキーが含まれます。パスワードは平文で保存されるため、このファイルのアクセス権限管理には注意が必要です（ただし、ファイル自体がユーザー専用ディレクトリ内に作成されます）。
*   ウィジェット起動時にこのJSONファイルが読み込まれ、左ペインのリストが構築されます。

### データベース接続処理
*   バックエンドでは、Pythonの強力なORMライブラリであるSQLAlchemyを使用してデータベースエンジン (`sqlalchemy.engine.Engine`) を作成します。
*   選択されたDBタイプとフォームに入力された情報に基づいて、適切なデータベース接続URI (Uniform Resource Identifier) が動的に生成されます。
    *   例 (PostgreSQL): `postgresql://username:password@host:port/database`
    *   例 (BigQuery, ADC使用): `bigquery://project_id`
    *   例 (BigQuery, サービスアカウントキー使用): `bigquery://project_id?credentials_path=/path/to/your/credentials.json`
*   `Connect` ボタンクリック時や「Auto-test connection before saving」が有効な場合の保存時には、実際にデータベースに対して非常に軽量なクエリ（例: `SELECT 1`）を実行することで、接続の可否をテストします。

### Google BigQuery認証 (gcloud CLI連携)
*   ユーザーが `Run gcloud auth application-default login` ボタンをクリックすると、ウィジェットはOSのプロセスとして `gcloud auth application-default login` コマンドを非同期で実行します。
*   この `gcloud` コマンドは、Google Cloud SDKの標準的な機能であり、ユーザーをWebブラウザ経由でGoogle Cloudに認証させ、ローカルマシンにアプリケーションデフォルト認証情報 (ADC) をキャッシュします。このADCは、`GOOGLE_APPLICATION_CREDENTIALS` 環境変数が設定されていない場合や、コード内で明示的に認証情報が指定されていない場合に、Google Cloudクライアントライブラリによって自動的に使用されます。
*   コマンド実行中は、進捗ダイアログが表示され、ユーザーはキャンセルも可能です。タイムアウトはデフォルトで60秒に設定されています。
*   認証が成功すると、ADCが更新され、以降「Credentials Path」が空のBigQuery接続ではこのADCが利用されます。

### UIの動的制御とインタラクション
*   「DB Type」コンボボックスの値が変更されると、`QStackedWidget` を使用して表示されるフォームのページが切り替わり、選択されたDBタイプに適した入力フィールド群が表示されます。
*   左ペインの検索ボックスへの入力は、`QLineEdit` の `textChanged` シグナルに接続され、`QListWidget` の各アイテムの表示/非表示を切り替えることでリアルタイムフィルタリングを実現しています。

### エラーハンドリングとユーザーフィードバック
*   データベース接続試行時の一般的なエラー（例: ホスト名解決不可、ポート到達不能、認証失敗、データベース不存在など）、JSONファイルの読み書きエラー、`gcloud` コマンド実行時のエラーなどは捕捉され、ウィジェット下部のエラーメッセージ表示エリアや、Orangeフレームワーク標準の警告/エラーポップアップを通じてユーザーに通知されます。

### 出力データの構造 (`DBConnection` オブジェクト)
*   接続に成功すると、`DBConnection` というカスタムクラスのインスタンスがウィジェットの出力ポートから送信されます。このオブジェクトは以下の主要なプロパティ（属性）を持ちます。
    *   `name` (str): ユーザーが設定した接続名。
    *   `db_type` (str): 接続されたデータベースの種類 (例: "PostgreSQL")。
    *   `engine` (SQLAlchemy `Engine`): 実際にデータベースとの通信に使用されるSQLAlchemyのEngineインスタンス。このEngineは、後続のデータ取得ウィジェット（例: SQL Executor）に渡され、クエリの実行や結果のフェッチに使用されます。
    *   `last_connected` (str or None): 最後にこの接続設定で正常に接続した日時（ISO 8601形式の文字列）。まだ接続されていない場合は `None`。