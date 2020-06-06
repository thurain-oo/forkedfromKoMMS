---
title: Making SQL Queries
author: minn
type: post
date: 2012-02-16T08:36:37+00:00
url: /2012/02/16/making-sql-queries/
categories:
  - General
  - Java
  - PHP
  - Python
tags:
  - sql

---
This article is to summarize how do we make SQL queries using different languages and their methods. Making connection to the database will not be covered here.

In the examples, we will be querying &#8220;SELECT * FROM employees where eid =and dept =&#8221;

It is not recommended to use SQL statements without placeholders in order to reduce the risk of SQL injection.

## Java

In Java, we can use JDBC, Hibernate, or some other database frameworks to interact with databases. Generally, I would prefer to use methods that allow me to insert &#8220;values&#8221; into an sql query. With JDBC, we can use PreparedStatement method. There is also createStatement method, where you insert user-supplied values into the query directly.

Basically the flow looks like this:

  1. Get a &#8220;Connection&#8221; object, with DriverManager
  2. From &#8220;Connection&#8221; object, we create a &#8220;Statement&#8221; object with sql statement.
  3. From the statement object, we generate &#8220;ResultSet&#8221;.

```java
int employeeId = 512 // we will query for employee with id 512
int deptId = 206 // from department 206

Connection connection = DriverManager.getConnection(...); // please fill in
PreparedStatement statement = connection.prepareStatement("SELECT * FROM employees WHERE eid = ? AND dept = ?");
statement.setInt(1, employeeId); // use setString if the argument is a String
statement.setInt(2, deptId);
ResultSet rs = statement.executeQuery();
while (rs.next()) { // Iterate the result set
// ...
}
```


## PHP

PHP has more methods to interact with database. It also depends on the module the php interpreter is built with. We can use MySQL extension, PostgreSQL extension, or ADODB or PDO as generic abstraction interfaces.

**With mysqli extension**

```php
$db = new mysqli('localhost', 'user', 'password', 'world');
$statement = $db->prepare('SELECT * FROM employees WHERE eid = ? AND dept = ?');
$statement->bind_param('dd', $employeeId, $deptId); // types and variables

$employeeId = 512;
$deptId = 206;

$statement->execute();
```

NOTE: Why mysqli instead of mysql command? If we are using MySQL v4.1.3 and above, PHP manual recommends to use mysqli which is an improved version. <a title="mysqli overview" href="http://php.net/manual/en/mysqli.overview.php" target="_blank">Reference</a>.

**With PostgreSQL extension**

```php
$db = pg_connect("dbname=world");

$employeeId = 512;
$deptId = 206;

$result = pg_query_params($db, 'SELECT * FROM employees WHERE eid = ? AND dept = ?', array($employeeId, $deptId));
```

**With ADODB**

```php
$db = NewADOConnection('mysql');
$db->Connect('localhost', 'user', 'password', 'world');
// Can also use $db = NewADOConnection('mysql://$user:$pwd@$server/$db?persist")
$rs = $db->execute('SELECT * FROM employees WHERE eid = ? AND dept = ?', array($employeeId, $deptId));
while($array = $rs->FetchRow()) {
//...
}
```

**With PDO**

```php
$db = new PDO('mysql:host=$host;dbname=$dbname', $user, $pass);
$employeeId = 512;
$deptId = 206;

// We will use unamed placeholders.
$statement = $db->prepare('SELECT * FROM employees WHERE eid = ? AND dept = ?');
$statement->bindParam(1, $employeeId);
$statement->bindParam(2, $deptId);
$statement->execute();
// ...
```