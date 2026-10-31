# Cyber Attack Detection - Flask App

Run: `pip install -r requirements.txt` then `python app.py`

Needs a local MySQL database `cyber_attack` with a `user` table
(Name, Email, Password, Age, Address, Contact).
`/prediction` needs `DecisionTreeClassifier.sav` in the project folder.
The Neural Network option only shows a stored accuracy, so Keras/TensorFlow are not required.

Changes from the printed report: `np.float` -> `float`; added `/logout` and
the `app.run()` block. Login/registration SELECT queries still use string
formatting (as printed) and are open to SQL injection; use `%s` placeholders
with `cur.execute(sql, (a, b))` if this is ever used beyond a class project.
