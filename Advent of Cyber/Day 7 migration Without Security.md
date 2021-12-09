# Migration Without Security
Learning Objectives  

-   What is NoSQL?
-   Understanding NoSQL database
-   Understand Why NoSQL happens
-   Understand what NoSQL injection is
-   Using NoSQL Injection to bypass a login form

#### What is NoSQL
Refers to a database that is a non-relational database and is short for `non SQL` and 	`Not only SQL`. It is a data-storing and data-retrieving system. Commonly used nowadays for big Data and IoT devices.

#### Understanding NoSQL
This task covers `MongoDB` dababase, a free and popular document-store NoSQL database. It consists of databases, tables, fields but with different names for these:
- **Collections** are similar to tables or views in MySQL
- **Documents** are similar to rows or records in MySQL
- **Fields** are similar to columns in MySQL

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d617515c8cd8348d0b4e68f/room-content/753375399860b87237241fbd9ab2af72.png)

Also, it is useful to look at the query operators:
- `$and` equivalent to `AND` in MySQL
- `$or` equivalent to `OR` in MySQL
- `$eq` equivlent to `=` in MySQL

#### Interacting with MongoDB Server
MongoDB is used with the default port of `27017`.

Commands
- `mongo` gets into the mongoDB shell
- `show databases` shows databases
- `use <database>` enters the database provided, creates one if it doesn't exist yet
- `db.createCollection()` creates a new collection in the database
- `db.<collection>.insert()` inserts a new document into the collection provided
- MongoDB automatically creates a unique ID called `_id` for each document within the collection.
- `db.<collection>.update` updates the document
- `db.<collection>.remove()` to remove a a document from a collection
- `db.<collection.drop()` to remove/drop the collection

#### What is NoSQL injection?
NoSQL injection is a web security vulnerability that allows the attacker to have control over the database. A NoSQL injection happens by sending queries via untrusted and unfiltered web application input, which leads to leaked unauthorized information. In addition, the attacker can use the NoSQL injection to perform various operations such as modifying data, escalating privileges, DoS attacks, and others.

#### Bypassing login pages
The following is the query that is used in web apps: `db.users.find({query})` or `db.users.findOne({query})` functions where the `query` is JSON data that's sent via the application: `{"username": "admin", "password":"adminpass"`.

MongoDB Operators
- `$eq` - matches records that equal a certain value
- `$ne` - matches records that are NOT equal to a certain value
- `$gt` - matches records that are greater than a certain value
- `$where` - matches records based on Javascript condition
- `$exists` - matches records that have a certain field
- `$regex` - matches records that satisfy certain regular expressions

#### Exploiting NoSQL injection
First, need to find an entry point that doesn't sanitize the user's input. Sometimes, the web app accepts user input via `GET` or `POST` queries, and sometimes it accepts a `JSON` object, as is the case with `APIs`.

Injecting a NoSQL query is different based on if its a `GET` or `POST` or `JSON` objects but its the same concept. If its a `GET` or `POST` request, you inject an array of the MongoDB operator to match the JSON objection to match the `Key:Value`. URL example:

```scheme
http://example.thm.labs/search?username=admin&role[$ne]=user
```

##### Question 1 
Navigate to the `flagdb` and use the command `db.getCollectionNames()` and then find the flag

##### Question 2
Use burp suite to intercept the POST request for the login page and modify the HTTP paramters to `username=admin&password[$ne]=admin` to bypass the login and go to `/dashboard`.

THM{b6b304f5d5834a4d089b570840b467a8}

##### Question 3
Change the URL HTTP paramters to `username[$ne]=admin&role=guest` to get all the guest accounts

THM{2ec099f2d602cc4968c5267970be1326}

##### Question 4
The user `mcskidy` is of the admin role so modify the URL HTTP parameters to be `username=mcskidy&role=admin` to get his details

ID:6184f516ef6da50433f100f4:mcskidy:admin