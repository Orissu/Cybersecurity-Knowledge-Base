# SQL Injection

The Structured Query Languqge Injection is an attack on a web application database server that causes malicious queries to be executed.
A Database is a way of electronically store data in an organised way. It is controlled by a DataBase Management System (DBMS)
There are two types of DBMS : relational and non-relational.

A relational database stores information in tables and often share informtion between the tables. It contains a primary key and a secondary key to link the tables between them

A non-relational database is a database that doesn't use tables. A specific database layout doesn't need to be constructed so each row of data can contain different information giving more flexibility over a relational database.

SQL is used to query databases. SQL queries are called statements.
Here is a list of Statements.

| SELECT | Used to retrieve something from a database |
| LIMIT NUMBER | Forces the database to return a maximum NUMBER of rows |
| where | used to put a condition |
| like % | used to retrieve information that isn't an exact match (we can use the wildcard %)|
| UNION | combines the result of 2 or more SELECT statements |
| INSERT into |adds a row to the table |
| update user SET | update the table user |
| DELETE | Deletes the row from the table |

## What is an SQLi ?

The point wherein an application using SQL can turn into a SQL injection is when user-porivded data gets included in the sql query.
`--` : everything after that is a comment      `;`: end of statement

## In band SQLi

Refers to the same method of communication being used to exploit the vulnerability and also retrieve the results.

## Error-based SQLi

Error messages from the database are printed directly to the browser screen allowing us to see the error.

## Union-based SQLi

Uses the SQL UNION operator alongside a SELECT.

## Blind SQLi

When we get little to no feedback to confirm whether our SQL queries were successful or not.

## Boolean Based SQLi

Refers to the response we receive (a boolean). It confirms if our SQLi wqs successful or not.

## Time Based SQLi

It is the same as the boolean based SQLi but thre's isn't any visual indicator. The indicator is the time the query takes to complete.

## Out of Band SQLi

There are two communication channels : 1 to launch the attack and the other to gather the results.
The hacker request and SQLi with an injected payload to the website, then the website makes a statement to the database passing the payload with it. Finally the database sends data through a HTTP request to the hacker.
