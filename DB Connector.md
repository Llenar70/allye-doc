# DB Connector

## Overview
The DB Connector widget provides an interface to establish connections to various types of databases and manage their connection information. Utilizing the SQLAlchemy library, it supports major databases such as PostgreSQL, MySQL, SQL Server, Redshift, and BigQuery. It allows users to easily reuse saved connection information or quickly set up new connections.

## Input
This widget has no input data.

## Output
*   **Connection (`DBConnection`)**: A connection information object outputted upon successful connection.
    *   `name` (str): The user-defined name for the connection.
    *   `db_type` (str): The type of the connected database (e.g., "PostgreSQL", "BigQuery").
    *   `engine` (SQLAlchemy `Engine`): An SQLAlchemy Engine object that can be used for database operations. This Engine is utilized by subsequent widgets (e.g., SQL Executor widget) for query execution, etc.
    *   `last_connected` (str, Optional): The timestamp of the last successful connection (ISO 8601 format string).

## Feature Description
The DB Connector widget is designed to allow users to easily configure and manage database connections through a GUI.

![DB Connector Widget Overview](./imgs/DB_overview.png)
*Figure 1: Overview of the DB Connector widget. The settings area is on the left, and the connection list/details are on the right (Note: The image description in the original text seems to be a copy-paste error from the Binary Analysis widget. This description assumes the standard layout of a connection manager).*

Key features include:
*   **Connection to Diverse Databases**: Supports multiple database systems including PostgreSQL, MySQL, SQL Server, Redshift, and Google BigQuery.
*   **Persistent Connection Information**: Configured connection information is securely saved in a local JSON file (`~/.allye_secrets/connections.json`) and automatically loaded when the widget starts, facilitating easy reuse.
*   **Intuitive and Interactive UI**:
    *   Saved connections are listed with icons indicating the DB type.
    *   A search box allows for quick filtering of numerous saved connections.
    *   Required input fields are dynamically displayed based on the selected DB type.
    *   The password input field has a toggle button to show/hide the input.
*   **Connection Test Functionality**:
    *   Manually test connections using the `Connect` button.
    *   The "Auto-test connection before saving" option enables automatic connection testing before saving connection information.
*   **Real-time Connection Status Display**: The current state (connecting, connected, not connected, connection error) is clearly indicated with color and text. Response time is also displayed upon successful connection.
*   **Google BigQuery Authentication Support**:
    *   In addition to specifying the path to a service account key file (JSON), it also provides **authentication using the `gcloud` command-line interface (CLI)**.
    *   <span style="color:red; font-weight:bold;">Important: To use BigQuery's `gcloud` authentication feature (the `Run gcloud auth application-default login` button), the Google Cloud SDK (gcloud CLI) must be pre-installed on your computer, and the OS environment variable PATH must be correctly configured.</span>
*   **Connection Information Management**: Easily duplicate (Clone) or delete saved connection information from the GUI.

## UI Component Description

The DB Connector widget screen is broadly composed of a title bar, a left pane (Saved Connections), and a right pane (Connection Settings).

### 1. Title Bar
*   The orange bar at the top of the widget, displaying the widget name "DB Connector".

### 2. Left Pane: Saved Connections
*   **Header Label**: Displays "Saved Connections", indicating this section lists saved connection configurations.
*   **Search Box (`Search connections...`)**: Typing text here filters the connection list in real-time, showing only connections whose names contain the entered text.
*   **Connection List**:
    *   Saved connection settings are displayed in a list.
    *   Each item shows an icon representing the database type (e.g., BQ for BigQuery) and the connection name.
    *   Hovering the mouse cursor over an item displays detailed information such as connection name, DB type, and last connected time as a tooltip.
    *   Clicking an item in the list automatically loads its connection information into the form in the right pane.

### 3. Right Pane: Connection Settings
*   **Header Label**: Displays "Connection Settings", indicating this section is for configuring and editing connection information.
*   **DB Type**:
    *   Select the type of database to connect to from the dropdown list (PostgreSQL, MySQL, SQL Server, Redshift, BigQuery).
    *   Upon selection, an icon corresponding to the DB type is displayed, and the content of the connection details form below changes.
*   **Connection Details Form (content varies by DB type)**:
    *   **When a generic DB (PostgreSQL, MySQL, SQL Server, Redshift) is selected**:
        *   `Project ID`: (Not displayed in this form)
        *   `Host`: Hostname or IP address of the database server.
        *   `Port`: Port number of the database server (default value is entered when DB type is selected).
        *   `Database`: Name of the database to connect to.
        *   `Username`: Username for connecting to the database.
        *   `Password`: Password corresponding to the above username. A button on the right toggles visibility of the input.
        *   `Connection Name`: The identification name for saving this connection setting. Displayed in the list in the left pane.
    *   **When BigQuery is selected**:
        *   `Project ID`: Google Cloud project ID.
        *   `Credentials Path`: (Optional) Full path to the Google Cloud service account key JSON file. If empty, `gcloud` application default credentials (ADC) will be used.
        *   `Authenticate`:
            *   `Run gcloud auth application-default login` button: Clicking this button initiates the Google Cloud authentication process using the `gcloud` CLI tool. Typically, a browser will open, prompting for authentication with a Google account. If authentication is successful, ADC will be set or updated.
            *   <span style="color:red; font-weight:bold;">Again, this feature requires gcloud CLI to be installed and its PATH configured.</span>
        *   `Connection Name`: The identification name for saving this connection setting.
*   **Connection Options**:
    *   `Auto-test connection before saving` (checkbox): If checked, a connection test is automatically performed when saving connection information with the "Save / Update" button. If the test fails, the information is not saved, and an error is displayed.
*   **Status Display Area**:
    *   An indicator (colored circle) and a text message show the connection status.
        *   Green (● Connected): Connection successful. Response time is also shown in parentheses (e.g., `(1213 ms)`).
        *   Gray (● Not Connected): Not connected or disconnected state.
        *   Red (● Connection Error): Connection attempt failed.
        *   Blue (● Testing Connection...): Currently performing a connection test.
*   **Action Buttons**:
    *   `Connect` (Green): Attempts to connect to the database with the information currently entered in the form. If successful, the connection information (`DBConnection` object) is outputted from the widget, and the status display changes to "Connected".
    *   `Save / Update` (Orange): Saves or updates the information currently in the form to the connection list under the specified "Connection Name".
    *   `Clone` (Light Orange): Duplicates the connection information selected in the left pane. A new entry is created with a new connection name (usually the original name appended with "- Copy"), and its information is loaded into the form.
    *   `Delete` (Gray): Deletes the connection information selected in the left pane from the list. A confirmation dialog is displayed.
*   **Error Message Display Area**: If an error occurs during `Connect` or `Save / Update`, details are displayed in this area in red text.

## Usage Examples

![DB Connector Widget Usage Example](./imgs/DB_flow.png)

### Example 1: Create and connect to a new PostgreSQL database
1.  Open the DB Connector widget.
2.  In the right pane, select "PostgreSQL" from the "DB Type" dropdown.
3.  Enter the PostgreSQL server's "Host" (e.g., `localhost`), "Port" (e.g., `5432`), "Database" (e.g., `mydatabase`), "Username" (e.g., `myuser`), and "Password" in their respective fields.
4.  Enter an arbitrary name in "Connection Name" (e.g., `My Local PostgreSQL`).
5.  (Optional) Check "Auto-test connection before saving".
6.  Click the `Save / Update` button. The connection information is added to the list in the left pane. If auto-test is enabled, a connection test is performed here.
7.  Click the `Connect` button. If the status display below the form turns green "Connected" and shows a response time, the connection is successful. At this point, a `DBConnection` object is sent from the widget's output port.

### Example 2: Connect to Google BigQuery using gcloud CLI authentication
<span style="color:red; font-weight:bold;">Prerequisite: Google Cloud SDK (gcloud CLI) must be installed in your environment, and its PATH must be correctly configured.</span>
1.  In the right pane, select "BigQuery" from the "DB Type" dropdown.
2.  Enter your Google Cloud project ID in "Project ID" (e.g., `my-gcp-project-12345`).
3.  Leave the "Credentials Path" field empty (to use application default credentials).
4.  Click the `Run gcloud auth application-default login` button.
    *   If an information dialog appears, review its content and click "Ok".
    *   The system's default browser will launch, displaying a page to authenticate with your Google account. Follow the on-screen instructions to complete authentication.
    *   Once the authentication process is complete, a success or failure message will be displayed within the widget.
5.  Enter an arbitrary name in "Connection Name" (e.g., `My BigQuery ADC`).
6.  Click the `Save / Update` button to save the connection information.
7.  Click the `Connect` button. If the status display shows "Connected", the connection is successful.

### Example 3: Reuse a saved connection setting
1.  When the widget opens, previously saved connections are displayed in the list in the left pane.
2.  Click the desired connection name from the list (e.g., `My Local PostgreSQL`).
3.  The information for the selected connection is automatically loaded into the form in the right pane.
4.  Click the `Connect` button to reconnect to the database.

### Example 4: Duplicate an existing connection setting and modify parts of it
1.  Select the source connection setting from the list in the left pane.
2.  Click the `Clone` button in the right pane.
3.  A new connection entry, with "- Copy" (or "- Copy 2", etc., to avoid duplicates) appended to the original connection name, is created in the list in the left pane, and its information is loaded into the form in the right pane.
4.  Modify the information in the form as needed (e.g., specify a different database name, change the connection name to something more descriptive).
5.  After making changes, click the `Save / Update` button to save the new connection setting.

## Detailed Logic (Internal Operations)

### Saving and Loading Connection Information
*   All connection settings are saved in a JSON file named `connections.json` located in a hidden folder `.allye_secrets` within the user's home directory.
    *   Typical paths:
        *   Linux/macOS: `~/.allye_secrets/connections.json`
        *   Windows: `%USERPROFILE%\.allye_secrets\connections.json`
*   The file stores each connection setting as an array of objects. Each object contains keys such as `name`, `db_type`, `host`, `port`, `database`, `username`, `password` (for RDBs), `bq_project_id`, `bq_cred_path` (for BigQuery), `last_connected`, etc. Passwords are stored in plaintext, so care should be taken with the access permissions of this file (though the file itself is created within a user-specific directory).
*   This JSON file is read when the widget starts, and the list in the left pane is constructed.

### Database Connection Processing
*   On the backend, SQLAlchemy, a powerful ORM library for Python, is used to create a database engine (`sqlalchemy.engine.Engine`).
*   Based on the selected DB type and the information entered in the form, an appropriate database connection URI (Uniform Resource Identifier) is dynamically generated.
    *   Example (PostgreSQL): `postgresql://username:password@host:port/database`
    *   Example (BigQuery, using ADC): `bigquery://project_id`
    *   Example (BigQuery, using service account key): `bigquery://project_id?credentials_path=/path/to/your/credentials.json`
*   When the `Connect` button is clicked, or when saving with "Auto-test connection before saving" enabled, a very lightweight query (e.g., `SELECT 1`) is actually executed against the database to test connectivity.

### Google BigQuery Authentication (gcloud CLI Integration)
*   When the user clicks the `Run gcloud auth application-default login` button, the widget executes the `gcloud auth application-default login` command as an OS process asynchronously.
*   This `gcloud` command is a standard feature of the Google Cloud SDK. It authenticates the user with Google Cloud via a web browser and caches Application Default Credentials (ADC) on the local machine. These ADCs are automatically used by Google Cloud client libraries if the `GOOGLE_APPLICATION_CREDENTIALS` environment variable is not set or if credentials are not explicitly specified in the code.
*   During command execution, a progress dialog is displayed, and the user can cancel it. The timeout is set to 60 seconds by default.
*   Upon successful authentication, the ADC is updated, and subsequent BigQuery connections with an empty "Credentials Path" will use this ADC.

### Dynamic UI Control and Interaction
*   When the value of the "DB Type" combobox changes, the page of the form displayed (using `QStackedWidget`) switches to show the input fields appropriate for the selected DB type.
*   Input in the search box in the left pane is connected to the `textChanged` signal of `QLineEdit`, enabling real-time filtering by toggling the visibility of each item in the `QListWidget`.

### Error Handling and User Feedback
*   Common errors during database connection attempts (e.g., hostname not resolvable, port unreachable, authentication failure, database non-existent), errors in reading/writing the JSON file, and errors during `gcloud` command execution are caught and notified to the user via the error message display area at the bottom of the widget or through standard Orange framework warning/error popups.

### Output Data Structure (`DBConnection` Object)
*   Upon successful connection, an instance of a custom class `DBConnection` is sent from the widget's output port. This object has the following main properties (attributes):
    *   `name` (str): The connection name set by the user.
    *   `db_type` (str): The type of the connected database (e.g., "PostgreSQL").
    *   `engine` (SQLAlchemy `Engine`): The SQLAlchemy Engine instance actually used for communication with the database. This Engine is passed to subsequent data retrieval widgets (e.g., SQL Executor) to be used for executing queries and fetching results.
    *   `last_connected` (str or None): The timestamp of the last successful connection with this setting (ISO 8601 formatted string). `None` if not yet connected.