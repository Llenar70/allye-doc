# Google Sheets

A widget for connecting to Google Sheets to import and export data.

## Prerequisites

**Important:** To use this widget, **Google Cloud SDK (gcloud CLI) must be installed on your computer, and the path to the gcloud command must be set in your PATH environment variable.**
For information on installing the Google Cloud SDK, please refer to the [official documentation](https://cloud.google.com/sdk/docs/install).

The authentication process will execute the `gcloud auth login` command, prompting for Google account authentication via a browser.

## Inputs

*   **Data**
    *   **Type:** `Orange.data.Table`
    *   **Description:** The dataset to export to Google Sheets.
    *   **Example Input Data:**
        Table data outputted from Orange's `File` widget or other data processing widgets.
        For example, a format like the Iris dataset:

        | sepal length | sepal width | petal length | petal width | iris        |
        | :----------- | :---------- | :----------- | :---------- | :---------- |
        | 5.1          | 3.5         | 1.4          | 0.2         | Iris-setosa |
        | 4.9          | 3.0         | 1.4          | 0.2         | Iris-setosa |
        | ...          | ...         | ...          | ...         | ...         |

## Outputs

*   **Data**
    *   **Type:** `Orange.data.Table`
    *   **Description:** Data imported from the connected Google Spreadsheet. The first row of the spreadsheet is treated as the header. Numerical data is automatically interpreted as a numeric type.

## UI and Feature Description

This widget provides a UI for Google account authentication, connecting to/creating spreadsheets, and configuring data synchronization settings.

![Google Sheets overview](./imgs/gseets_overview.png)

1.  **Google Spreadsheet Connector (Header)**
    *   The widget title is displayed. It has an orange background.

2.  **Google Account**
    *   **gcloud auth login ... command display area:** The gcloud CLI command used for authentication is displayed (non-editable).
        Default command: `gcloud auth login --enable-gdrive-access --update-adc --force`
    *   **Execute Command Button:** Clicking this button executes the displayed gcloud CLI command.
        *   A terminal or command prompt will launch (in the background), and a browser will open asking you to log in to your Google account and grant permissions.
        *   If authentication is successful, the widget gains access to the Google Sheets API and Google Drive API.
        *   The button is disabled and displays "Executing..." during authentication.
        *   A progress dialog with a cancel button is displayed during the authentication process.

3.  **Spreadsheet Settings**
    *   **Existing Spreadsheet URL:**
        *   **URL input field:** Enter the URL of an existing Google Spreadsheet you want to connect to. (e.g., `https://docs.google.com/spreadsheets/d/SPREADSHEET_ID/edit#gid=SHEET_ID`)
        *   **Connect Button:** Connects to the spreadsheet at the entered URL. If the URL includes a `gid` parameter, it connects to that specific sheet. Otherwise, it connects to the first sheet.
        *   **Open Button:** Opens the currently connected spreadsheet in the default web browser.
    *   **Create New Spreadsheet:**
        *   **Filename input field:** Enter the filename for the new spreadsheet to be created. (Default: `My Orange3 Data`)
        *   **Sheet Name input field:** Enter the name of the first sheet within the new file. (Default: `Sheet1`)
        *   **Create Sheet Button:**
            *   If a spreadsheet with the specified filename already exists on Google Drive:
                *   Connects to that spreadsheet.
                *   If the specified sheet name already exists, all data in that sheet will be cleared.
                *   If the specified sheet name does not exist, it will be created as a new sheet.
            *   If a spreadsheet with the specified filename does not exist:
                *   Creates a new spreadsheet with the specified filename and sheet name.
            *   After creation or connection, if data is connected to the Inputs, that data will be exported.

4.  **Sync Settings**
    *   **Auto-refresh interval:** Sets the interval in minutes for automatically reloading data from the spreadsheet. (1-60 minutes)
    *   **Export Data Button:** Exports the data currently connected to the widget's Inputs to the specified sheet of the connected spreadsheet. Existing data in the sheet is cleared before export.
    *   **Refresh Now Button:** Manually reloads data from the connected spreadsheet and sends it to the Outputs.
    *   **Auto-sync: OFF/ON Button:** Toggles the auto-refresh feature on/off. When ON, `Refresh Now` is executed automatically at the set interval.

5.  **Status**
    *   Displays the current state of the widget (e.g., "Status: Connected to Google Sheets API", "Status: Authentication failed") or operation results.
    *   The background color changes according to the status type:
        *   Greenish: Success (e.g., connection successful, data sync complete)
        *   Reddish: Error (e.g., authentication failed, connection failed)
        *   Yellowish: Warning (e.g., no data in sheet)
        *   Bluish: Executing (e.g., gcloud command executing)
        *   Grayish: Informational (e.g., not connected)

## Main Operational Flow

### 1. Google Account Authentication
1.  Click the **Execute Command** button in the "Google Account" section.
2.  A browser will launch, prompting you to log in to your Google account and grant access to Google Drive and Google Sheets. Follow the on-screen instructions to complete authentication.
3.  If authentication is successful, the status will display "Authentication successful" or "Connected to Google Sheets API".

### 2. Connecting to an Existing Spreadsheet and Retrieving Data
1.  After authentication, paste the URL of the desired spreadsheet into the "Existing Spreadsheet URL" field in the "Spreadsheet Settings" section.
2.  Click the **Connect** button.
3.  If the connection is successful, the spreadsheet data is loaded and becomes available from the widget's Outputs. The status will show connection information and sync time.
4.  Use the **Refresh Now** button for manual updates or enable auto-sync as needed.

### 3. Creating a New Spreadsheet and Exporting Data
1.  After authentication, enter the "Filename" and "Sheet Name" in the "Create New Spreadsheet" section under "Spreadsheet Settings".
2.  Click the **Create Sheet** button.
3.  A new spreadsheet is created on Google Drive (or an existing one is used).
4.  If data is connected to the widget's Inputs, it will be automatically exported to the new sheet. The status will display the creation/export result.
5.  The URL of the created spreadsheet will then be automatically filled into the "Existing Spreadsheet URL" field, and it will be in a connected state.

### 4. Exporting Data (Manual)
1.  Connect Orange table data to the widget's Inputs.
2.  Ensure you are connected to a spreadsheet (or have created a new one).
3.  Click the **Export Data** button in the "Sync Settings" section.
4.  Existing data in the connected sheet will be cleared, and the input data will be written to the spreadsheet.

## Detailed Logic

*   **Authentication (`execute_gcloud_command`)**:
    *   Executes the `gcloud auth login --enable-gdrive-access --update-adc --force` command as a subprocess. This updates the Application Default Credentials (ADC) stored in the user's local environment, obtaining the necessary credentials to access Google APIs.
    *   The process runs asynchronously, and the status is updated upon completion (success or failure). A timeout (30 seconds) is also set.
    *   After successful authentication, credentials are obtained using `google.auth.default()`, and service clients for Google Sheets API (`build('sheets', 'v4', ...)`) and Google Drive API (`build('drive', 'v3', ...)`) are initialized.

*   **Connecting to Existing Sheet (`connect_to_spreadsheet`)**:
    *   Extracts the spreadsheet ID from the input URL using a regular expression (`/d/([a-zA-Z0-9-_]+)`). Also extracts the sheet ID (gid) from `gid=(\d+)` to identify the target sheet.
    *   Retrieves spreadsheet metadata (sheet names, etc.) using the Google Sheets API `spreadsheets().get()` method and updates the connection status.
    *   Then, `refresh_data()` is called to read the sheet's data.

*   **Creating New Sheet (`create_new_spreadsheet`)**:
    *   First, uses the Google Drive API `files().list()` method to search for an existing spreadsheet with the specified filename.
    *   **If an existing file is found**: Uses its spreadsheet ID. Retrieves the list of sheets using the Google Sheets API to check if the specified sheet name exists. If it exists, `_clear_sheet_data()` is called to clear its data. If it doesn't exist, a new sheet is added using `spreadsheets().batchUpdate()`.
    *   **If no existing file is found**: Creates a new spreadsheet with the specified filename and sheet name using the Google Sheets API `spreadsheets().create()` method.
    *   After creation, if there is input data, `_write_data_to_sheet()` is called to write the data. Finally, `connect_to_spreadsheet()` is called to formally connect to the created sheet and read its data.

*   **Data Retrieval (`refresh_data`)**:
    *   Uses the Google Sheets API `spreadsheets().values().get()` method to retrieve cell data from the connected spreadsheet ID and sheet name.
    *   Converts the retrieved data (list of lists of values) into a Pandas DataFrame. The first row is used as the header.
    *   Checks the data type of each column and converts columns that can be converted to numeric types using `pd.to_numeric`.
    *   Finally, converts the DataFrame to an Orange Table using `Orange.data.pandas_compat.table_from_frame()` and sends it to the widget's output.

*   **Data Export (`_export_data`, `_write_data_to_sheet`)**:
    *   First, calls `_clear_sheet_data()`, which uses the Google Sheets API `spreadsheets().values().clear()` to clear all data from the target sheet.
    *   Converts the input Orange Table to a Pandas DataFrame using `Orange.data.pandas_compat.table_to_frame()`. Categorical data is converted to strings, and NaN values are converted to empty strings.
    *   Formats the DataFrame's header and data rows into a list format and writes the data starting from cell A1 of the sheet using the Google Sheets API `spreadsheets().values().update()` method (`valueInputOption='RAW'`).

*   **Auto-Sync (`_toggle_auto_sync`, `_start_sync_timer`)**:
    *   Implemented using `QTimer`. When the `Auto-sync` button is turned ON, a timer starts with the specified "Auto-refresh interval" in milliseconds. The `refresh_data()` method is called each time the timer times out.

## Usage Example

1.  Add a `File` widget to your Allye workflow and load the data file you want to analyze (e.g., iris.tab).
2.  Add the `Google Sheets` widget to the workflow and connect the output (Data) of the `File` widget to the input (Data) of the `Google Sheets` widget.
3.  Open the `Google Sheets` widget and authenticate your Google account using the **Execute Command** button in the "Google Account" section.
4.  After authentication, in the "Spreadsheet Settings" section, enter "MyIrisData" for "Filename" and "IrisSheet1" for "Sheet Name", then click the **Create Sheet** button.
    *   This will create a spreadsheet named "MyIrisData" in your Google Drive, and within it, a sheet named "IrisSheet1", and the Iris data will be exported to it.
    *   A success message will appear in the status.
5.  Click the **Open** button to view the created spreadsheet in your browser.
6.  Edit some values in the spreadsheet in your browser.
7.  Return to Orange's `Google Sheets` widget and click the **Refresh Now** button.
8.  Connect a `Data Table` widget or similar to the output of the `Google Sheets` widget to confirm that the changes made in the spreadsheet are reflected in Orange.
9.  Alternatively, if "Auto-sync" is ON, changes will be reflected automatically at the specified interval.