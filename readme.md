# PostgreSQL Installation and Setup on Windows Without Admin Rights

## Preface

- Setting up PostgreSQL on a Windows machine often requires administrative permissions, which not everyone has access to. This guide shows the process of installing PostgreSQL using zipped binaries.
- This method is particularly useful for setting up a development or trial environment where administrative permissions are not required. By following these instructions, you can quickly get PostgreSQL up and running for development or testing purposes without administrative permissions.
- Please note that this setup should be considered temporary. For a proper PostgreSQL database setup, including production environments, you must ensure that all necessary permissions, security configurations, and best practices are followed.

## Contents of this Guide

- This guide provides step-by-step instructions for installing and setting up PostgreSQL on a Windows machine **without requiring administrative privileges**.
- The instructions cover:
  - Downloading binaries
  - Initializing the database
  - Starting and stopping the server
  - Accessing the SQL command line
  - Importing data into your new database
- By following these steps, you will have a fully functional PostgreSQL installation with some sample data.

## Pre-Installation Setup

- Before the actual installation of PostgreSQL, we create some shortcuts and folders.

### Download PostgreSQL Binaries

- Download the PostgreSQL binaries from [EnterpriseDB](https://www.enterprisedb.com/download-postgresql-binaries).
- Extract the downloaded archive to a desired folder (e.g., `C:\pgsql`).

### Directory Structure

- Ensure you have `C:\pgsql\bin` and other directories after extraction.
- Create a new folder inside `C:\pgsql` and name it `colony` or any other name you like.

### Scripts

1. **Database Initialization Script:**

   - Open Notepad and paste:

     ```batch
     C:\pgsql\bin\initdb -D C:\pgsql\pgdata -U postgres -W -E UTF8 -A scram-sha-256
     ```

   - Save the file as `pg_init.bat` inside the `colony` folder.
   - Breakdown of the arguments used:
     - `-D C:\pgsql\pgdata`: Specifies the directory where the database cluster will be stored.
     - `-U postgres`: Defines the username of the database superuser.
     - `-W`: Prompts for a password for the new database superuser.
     - `-E UTF8`: Sets the encoding for the database to UTF-8.
     - `-A scram-sha-256`: Configures the authentication method for local connections to use SCRAM-SHA-256.

2. **Start Database Script:**

   - Open Notepad and paste:

     ```batch
     C:\pgsql\bin\pg_ctl -D C:\pgsql\pgdata start
     ```

   - Save the file as `pg_start.bat` in the `colony` folder.

3. **Stop Database Script:**

   - Open Notepad and paste:

     ```batch
     C:\pgsql\bin\pg_ctl -D C:\pgsql\pgdata stop
     ```

   - Save the file as `pg_stop.bat` in the `colony` folder.

4. **Access SQL Command Line Script:**

   - Open Notepad and paste:

     ```batch
     C:\pgsql\bin\psql -U postgres
     ```

   - Save the file as `pg_sql.bat` in the `colony` folder.

### Shortcut to PostgreSQL Admin

- Create a shortcut in the `colony` folder pointing to `C:\pgsql\pgAdmin 4\runtime\pgAdmin4.exe`.

## PostgreSQL DB Installation

1. Initialize the PostgreSQL database by running `pg_init.bat`.
   - Enter the desired password when prompted.
2. Start the server by running `pg_start.bat`.
3. Access the SQL command line by running `pg_sql.bat`.

   - Verify the installation by running the command:

     ```sql
     SELECT version();
     ```

4. You are all set.

## Using Your New Database

- Assuming you have followed all the setup steps till now, you have a working PostgreSQL server.
- Now using the batch files we created (`pg_start.bat`, `pg_stop.bat`, and `pg_sql.bat`), you can start, stop, and interact with your PostgreSQL database without needing to run complex commands each time.
- See more details below.

### Starting the PostgreSQL Server

To start the PostgreSQL server, execute the `pg_start.bat` file:

1. Navigate to the `colony` folder where you saved the batch files.
2. Double-click on `pg_start.bat`.

   - This will start the PostgreSQL server, making it ready for accepting connections and running queries.

### Stopping the PostgreSQL Server

When you need to stop the PostgreSQL server, execute the `pg_stop.bat` file:

1. Navigate to the `colony` folder.
2. Double-click on `pg_stop.bat`.

   - This will gracefully stop the PostgreSQL server, ensuring all active connections are closed properly.

### Accessing the SQL Command Line

To access the PostgreSQL SQL command line tool, use the `pg_sql.bat` file:

1. Navigate to the `colony` folder.
2. Double-click on `pg_sql.bat`.

   - This will open the PostgreSQL command line interface (psql), where you can execute SQL commands directly.

## Loading Data into a PostgreSQL Database

This section covers three different ways to populate your new PostgreSQL database with data:

1. Prepare a Sample CSV File and Import Data from Command Line
2. Use pgAdmin UI to Run a Query and Insert Records Directly
3. Load Data Using Pagila Database in pgAdmin

### 1. Prepare a Sample CSV File and Import Data from Command Line

#### Create and Populate the CSV File

1. Open a text editor or a spreadsheet application.
2. Create a CSV file named `emp.csv` with the following content:

   ```plaintext
   emp_name,emp_age
   Locke,37
   Hobbes,73
   Rousseau,66
   Voltaire,84
   Montesquieu,65
   ```

3. Save the file in a directory, e.g., `C:\pgsql\upload\emp.csv`.

#### Import Data Using Command Line

1. Open your terminal or command prompt.
2. Connect to your PostgreSQL database using `psql` or the query editor in pgAdmin.
3. Run the following command to create the `emp_table` and import the data:

   ```sql
   CREATE TABLE emp_table (
     id serial NOT NULL,
     emp_name character varying(50),
     emp_age numeric(5),
     CONSTRAINT emp_pkey PRIMARY KEY (id)
   );

   COPY emp_table (emp_name, emp_age)
   FROM 'C:\pgsql\upload\emp.csv' DELIMITER ',' CSV HEADER;
   ```

- **NOTE**:
  - This method is quick and efficient for large datasets. Ensure the CSV path is correct and accessible.
  - When creating your CSV files, ensure the data types are consistent with your table schema to avoid import errors.

### 2. Use pgAdmin UI to Run a Query and Insert Records Directly

#### Set Up pgAdmin

1. Launch pgAdmin using the shortcut.
2. Create a new server:
   - Right-click on the Servers node and select `Create > Server…`.
   - Enter the following details:
     - **Name**: PostgreSQL
     - **Host**: `localhost`
     - **Port**: `5432`
     - **Username**: `postgres`
     - **Password**: `[your_password]`
   - Click Save.

#### Insert Records via Query Tool

1. Open the Query Tool by right-clicking on the database and selecting `Query Tool`.
2. Run the following SQL commands to create the `emp_table` and insert records:

   ```sql
   CREATE TABLE emp_table (
     id serial NOT NULL,
     emp_name character varying(50),
     emp_age numeric(5),
     CONSTRAINT emp_pkey PRIMARY KEY (id)
   );

   INSERT INTO emp_table (emp_name, emp_age) VALUES
     ('Locke', 37),
     ('Hobbes', 73),
     ('Rousseau', 66),
     ('Voltaire', 84),
     ('Montesquieu', 65);

   SELECT * FROM emp_table;
   ```

- **NOTE**:
  - This method of using pgAdmin UI is user-friendly, especially for beginners.
  - It provides a visual interface to interact with your database.

### 3. Load Data Using Pagila Database in pgAdmin

- PostgreSQL offers [several sample databases](https://wiki.postgresql.org/wiki/Sample_Databases).
- In this section, we will use the Pagila database, a popular sample database designed to simulate a DVD rental store, for practice.

#### Download and Set Up Pagila

1. Save the [Pagila schema file](https://github.com/jOOQ/sakila/blob/main/postgres-sakila-db/postgres-sakila-schema.sql) to your local machine at `C:\pgsql\upload\postgres-sakila-schema.sql`.
2. Save the [Pagila data insertion script file](https://raw.githubusercontent.com/jOOQ/sakila/main/postgres-sakila-db/postgres-sakila-insert-data.sql) to your local machine at `C:\pgsql\upload\postgres-sakila-insert-data.sql`.

#### Create and Populate Pagila Database

- Once you have downloaded the necessary files as shown above, follow these steps to create and populate the Pagila database in pgAdmin:

1. **Create a New Database in pgAdmin:**

   - Open pgAdmin and connect to your PostgreSQL server.
   - Right-click on the `Databases` node in the Browser tree and select `Create > Database...`.
   - Name the new database `pagila` and click `Save`.

2. **Open the Query Tool:**

   - In the pgAdmin Browser tree, expand the `Databases` node and find the `pagila` database.
   - Right-click on the `pagila` database and select `Query Tool`.

3. **Run the Schema Creation Script:**

   - In the Query Tool, click the folder icon to open a file.
   - Navigate to `C:\pgsql\upload\postgres-sakila-schema.sql` and open the file.
   - Click the `Execute/Refresh` button (lightning bolt icon) to run the script and create the database schema.

4. **Run the Data Insertion Script:**

   - In the Query Tool, click the folder icon again to open another file.
   - Navigate to `C:\pgsql\upload\postgres-sakila-insert-data.sql` and open the file.
   - Click the `Execute/Refresh` button to run the script and insert the data into the database.

5. **Be Patient:**
   - Running these scripts may take some time depending on the performance of your machine. Be patient and wait for the execution to complete.

- By following these steps, you will have successfully loaded the Pagila database into your PostgreSQL server using pgAdmin.
- This database can now be used for querying and practice.

## Database Management

### Password Reset

- Start the database and use `pg_sql.bat` to enter interactive mode.
- To reset the password for the default user `postgres`:

  ```sql
  \password postgres
  ```

- Follow the prompts to set a new password.

### Database Backup

- To back up the entire database:

```sh
pg_dump -U postgres -F c -b -v -f "C:\pgsql\backup\my_backup_date.backup" database_name
```

- `-U postgres` specifies the user.
- `-F c` specifies the format (custom).
- `-b` includes large objects.
- `-v` enables verbose mode.
- `-f` specifies the file path for the backup.

### Database Restoration

- To restore a database from a backup file, use the `pg_restore` utility:

```sh
pg_restore -U postgres -d database_name -v "C:\pgsql\backup\my_backup_date.backup"
```

- `-d database_name` specifies the database to restore to.

### Database Performance Monitoring

- Use the `pg_stat_activity` view to monitor current database activities:

```sql
SELECT * FROM pg_stat_activity;
```

- For more detailed statistics, explore the `pg_stat_database` view:

```sql
SELECT * FROM pg_stat_database;
```

## Contributing

- If you find any issues in this repository, please follow one of the methods below to contribute:

1. **Open a Pull Request**:

   - Fork the repository to your own GitHub account.
   - Clone the forked repository to your local machine.
   - Create a new branch for your changes: `git checkout -b your-branch-name`.
   - Make the necessary changes and commit them: `git commit -m "Description of changes"`.
   - Push the changes to your forked repository: `git push origin your-branch-name`.
   - Open a pull request from your branch to the main repository's `main` branch.
   - Provide a detailed description of your changes in the pull request.

2. **Open an Issue**:

   - Navigate to the "Issues" tab of the repository.
   - Click on "New issue".
   - Provide a clear and concise title and description of the issue.
   - Add any relevant labels, if applicable.
   - Submit the issue.

3. **Start a New Discussion**:
   - Navigate to the "Discussions" tab of the repository.
   - Click on "New discussion".
   - Select the appropriate category for your discussion (e.g., Q&A, Ideas, etc.).
   - Provide a detailed title and description of the topic you want to discuss.
   - Submit the discussion.

- By following these steps, you help improve this guide. Thank you for your contributions!

## Conclusion

- By following these instructions, you have successfully installed and configured PostgreSQL on a Windows machine without admin rights.
- You can now manage your databases, reset passwords, and import data using both UI and command-line methods.
