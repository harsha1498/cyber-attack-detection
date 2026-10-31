# Cyber Attack Detection System

A web-based **Cyber Attack Detection System** built with Python and
Flask. The application provides user registration and login, CSV dataset
upload, data preprocessing, machine-learning model evaluation, and a
prediction page that uses a saved Decision Tree model to classify a
network connection as a cyber attack or non-attack.

> **Project status:** Academic/class-project implementation. Review the
> security and deployment notes before exposing this application to the
> internet or using it with real data.

## Features

-   User registration and login backed by MySQL.
-   CSV dataset upload through the web interface.
-   Preview of the first 100 rows of the uploaded dataset.
-   Basic preprocessing of categorical columns (`protocol_type`, `flag`,
    and `service`).
-   Train/test split for model evaluation.
-   Model evaluation options:
    -   Extra Trees Classifier
    -   Decision Tree Classifier
    -   Support Vector Classifier (SVC)
    -   K-Nearest Neighbors (KNN)
    -   AdaBoost Classifier
    -   Neural Network option that displays a stored accuracy value
-   Cyber-attack prediction using the included
    `DecisionTreeClassifier.sav` model.
-   Logout functionality.

## Technology Stack

-   **Language:** Python
-   **Web framework:** Flask
-   **Data processing:** pandas, NumPy
-   **Machine learning:** scikit-learn
-   **Database:** MySQL
-   **Model persistence:** Python pickle (`.sav` file)
-   **Frontend:** HTML and CSS templates

## Project Structure

The application expects a structure similar to the following:

``` text
Cyber-Attack-Detection/
├── app.py
├── DecisionTreeClassifier.sav
├── requirements.txt
├── README.md
├── templates/
│   ├── index.html
│   ├── about.html
│   ├── login.html
│   ├── registration.html
│   ├── load.html
│   ├── view.html
│   ├── preprocess.html
│   ├── model.html
│   └── prediction.html
└── static/
    └── css/
        └── style.css
```

Keep the template and static filenames consistent with the paths
referenced by your Flask application. The saved model file must be in
the project root for the current prediction code.

## Requirements

-   Python 3.9 or later is recommended.
-   MySQL Server.
-   pip.
-   A browser.

Install the Python dependencies:

``` bash
pip install -r requirements.txt
```

The current `requirements.txt` contains:

``` text
flask
pandas
numpy
scikit-learn
mysql-connector-python
```

For reproducible results, consider pinning dependency versions after
confirming the versions used in your working environment.

## Database Setup

The application connects to a local MySQL database named `cyber_attack`
and expects a table named `user`.

Create the database and table:

``` sql
CREATE DATABASE cyber_attack;

USE cyber_attack;

CREATE TABLE user (
    Name VARCHAR(100),
    Email VARCHAR(150) PRIMARY KEY,
    Password VARCHAR(255),
    Age INT,
    Address VARCHAR(255),
    Contact VARCHAR(20)
);
```

Update the MySQL connection settings in `app.py` to match your local
database configuration.

**Do not publish database passwords or application secret keys in a
public GitHub repository.** Move credentials and the Flask secret key
into environment variables before publishing or deploying the
application.

## Run the Application

1.  Clone the repository:

    ``` bash
    git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
    cd YOUR-REPOSITORY
    ```

2.  (Recommended) Create and activate a virtual environment:

    ``` bash
    python -m venv venv
    ```

    Windows:

    ``` bash
    venv\Scripts\activate
    ```

    macOS/Linux:

    ``` bash
    source venv/bin/activate
    ```

3.  Install dependencies:

    ``` bash
    pip install -r requirements.txt
    ```

4.  Create the MySQL database and `user` table using the SQL above.

5.  Configure the database connection and secret key in `app.py`.

6.  Confirm that `DecisionTreeClassifier.sav`, the `templates/`
    directory, and the `static/` directory are in the expected
    locations.

7.  Start Flask:

    ``` bash
    python app.py
    ```

8.  Open the local address shown by Flask in your browser. With the
    default Flask settings, this is:

    ``` text
    http://127.0.0.1:5000/
    ```

The current application enables Flask debug mode. Debug mode is for
local development only; disable it before any deployment.

## Application Workflow

1.  **Home:** Open the application.
2.  **Register:** Create an account.
3.  **Login:** Sign in using the registered account.
4.  **Load Dataset:** Upload a CSV file.
5.  **View Dataset:** Preview the uploaded data.
6.  **Preprocess:** Encode the categorical columns and split the data
    into training and testing sets.
7.  **Model:** Select one of the available algorithms to train and view
    its accuracy on the test set.
8.  **Prediction:** Enter the 41 network-traffic feature values and
    submit them for classification.
9.  **Logout:** End the current session.

## Dataset and Prediction Input

The dataset upload/preprocessing code expects the CSV to contain these
categorical columns:

-   `protocol_type`
-   `flag`
-   `service`

The preprocessing route treats all columns except the last column as
features and the last column as the target. The uploaded CSV therefore
needs to match the feature layout and target format expected by the
application.

The prediction form and saved model use the following 41 input features
in this order:

``` text
duration
protocol_type
service
flag
src_bytes
dst_bytes
land
wrong_fragment
urgent
hot
num_failed_logins
logged_in
num_compromised
root_shell
su_attempted
num_root
num_file_creations
num_shells
num_access_files
num_outbound_cmds
is_host_login
is_guest_login
count
srv_count
serror_rate
srv_serror_rate
rerror_rate
srv_rerror_rate
same_srv_rate
diff_srv_rate
srv_diff_host_rate
dst_host_count
dst_host_srv_count
dst_host_same_srv_rate
dst_host_diff_srv_rate
dst_host_same_src_port_rate
dst_host_srv_diff_host_rate
dst_host_serror_rate
dst_host_srv_serror_rate
dst_host_rerror_rate
dst_host_srv_rerror_rate
```

**Important:** Supply values in exactly this order and use the same
encoding and feature definitions used when the model was trained. The
application code comments describe prediction output `0` as a cyber
attack and `1` as no cyber attack. Verify this mapping against the
training dataset and saved model before relying on predictions.

## Notes and Limitations

-   The dataset upload route stores the uploaded dataset in
    application-level variables. Restarting the application clears that
    in-memory data.
-   The preprocessing route currently calculates a split percentage from
    the form, but the actual `train_test_split` call uses a fixed
    `test_size=0.3`.
-   The Neural Network option displays a hard-coded/stored accuracy
    value; it does not train a neural network in the current `app.py`.
-   The saved `.sav` model is loaded with pickle. Only load model files
    from sources you trust. Pickle files can execute code when loaded.
-   The prediction route expects numeric values for all 41 features.
    Categorical values must be encoded consistently with the model's
    training process.

## Security Checklist Before Publishing or Deployment

The uploaded application code is suitable as a learning prototype, but
it needs security improvements before public use:

-   Replace SQL string formatting in login and registration queries with
    parameterized queries to prevent SQL injection.
-   Hash passwords with a password-hashing library such as Werkzeug; do
    not store plain-text passwords.
-   Move database credentials and `app.secret_key` to environment
    variables. If any real credentials were committed to GitHub, rotate
    them.
-   Disable Flask debug mode outside local development.
-   Add input validation and error handling for uploaded files and
    prediction values.
-   Add file-size/type restrictions and avoid trusting uploaded CSV
    content.
-   Use a production WSGI server and HTTPS for deployment.
-   Do not commit private datasets, database dumps, virtual
    environments, or secrets.

## Future Improvements

-   Add robust validation for uploaded datasets and missing values.
-   Save preprocessing encoders and reuse them during prediction.
-   Make the train/test split setting configurable.
-   Add precision, recall, F1-score, confusion matrix, and ROC-AUC where
    appropriate.
-   Replace the stored Neural Network score with a real training and
    evaluation pipeline if that feature is required.
-   Improve authentication, session handling, and user feedback.
-   Add automated tests and deployment instructions.

## License

No license is specified yet. Add a license file (for example, MIT) only
if you intend to grant others permission to use, modify, and distribute
this project.

## Author

Add your name, institution, and project contact details here.
