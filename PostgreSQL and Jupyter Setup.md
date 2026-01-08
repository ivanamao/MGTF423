# PostgreSQL and Jupyter Setup
by Bhaavya Naharas and Ivana Mao
## Step 1: Install PostgreSQL
1. Download PostgreSQL
- Go to [https://www.postgresql.org/download/](https://www.postgresql.org/download/), download the installation 
package for your operating system 
2. During installation
- You'll be prompted to set a password for the default superuser "postgres", make sure to 
  remember that password (to make it easy to remember, I recommend set the password as "postgres" too)
- Keep the default port number: 5432
- Keep other settings as is when installing
3. Verify installation
- Open terminal (MacOS) or command prompt (Windows), run: 
```bash 
psql --version
```
- If it returns "psql (PostgreSQL) xxx", PostgreSQL is installed correctly

## Step 2: Create a Course-Specific Database
1. To open PostgreSQL shell, go to terminal (MacOS) or command prompt (Windows), run:
```bash
psql -U postgres
```
You'll be prompted to enter the password you set earlier, then press enter / return<br>
2.	Create a database for the course
```bash
CREATE DATABASE risk_analysis;
```
Make sure to include ";" which marks the end of the SQL statement. SQL keywords like `CREATE` and `DATABASE` are 
case-insensitive.<br>
3.	Connect to the database
```bash
\c risk_analysis
```
4.	Confirm connection
```bash
SELECT current_database();
```
It should return:
```text
 current_database 
------------------
 risk_analysis
(1 row)
```

## Step 3: Set Up Python Environment
1. Install Python for your OS (if not already installed) [https://www.python.org/downloads/](https://www.python.org/downloads/)
<br> Recommend Python 3.9 or later. To check your version, open a fresh Terminal / 
   Command Prompt and run:
```bash
python --version
```
If it doesn't work, change the executable `python` to `python3` (the same for below commands)<br>
2. Create a virtual environment
```bash
python -m venv risk_env
```
3. Activate the environment
- Mac/Linux:
```bash
source risk_env/bin/activate
```
- Windows:
```bash
risk_env\Scripts\activate
```
4. Install required packages
```bash
pip install jupyter pandas sqlalchemy psycopg2-binary
```

## Step 4: Launch Jupyter Notebook
1.	To start Jupyter, enter the following in Terminal / Command Prompt with risk_env activated. It will open in the 
      default browser
```bash
jupyter notebook
```
2. create a new notebook, name it something like "risk_analysis_setup.ipynb"
3. To confirm Python kernel is active, run the following in a cell `print("Jupyter is working")`

## Step 5: Connect Python to PostgreSQL
1. Import libraries we downloaded earlier in the notebook
```python
import pandas as pd
from sqlalchemy import create_engine
```
2. Create a database engine (<password> is the password you set for superuser postgres)
```python
engine = create_engine(
     "postgresql://postgres:<password>@localhost:5432/risk_analysis"
)
```
3. Test the connection
```python
with engine.connect() as conn:
     print("Connected successfully")
```

## Step 6: Run and Validate Queries from Notebook
1.	Create a test table
```python
query = """
CREATE TABLE IF NOT EXISTS test_table (
    id SERIAL PRIMARY KEY,
    value INTEGER
);
"""
with engine.begin() as conn:
    conn.exec_driver_sql(query)
```
2.	Insert sample data 
```python
query = """
INSERT INTO test_table (value) VALUES (10), (20), (30);
"""
with engine.begin() as conn:
    conn.exec_driver_sql(query)
```
3. Query the data
```python
df = pd.read_sql("SELECT * FROM test_table;", engine)
df
```
4.	Confirm output appears correctly
