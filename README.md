# pgbouncer-for-postgres

This blog post describes step-by-step how to improve the PostgreSQL Database Server architecture connections management, reduce the load on the PostgreSQL Server and improve the performance using the PgBouncer connection pooler. Here is a breakdown of the topics we will discover in this article

- How a PostgreSQL Database Server works
- Improving efficiency with PgBouncer poolers
- How to install and configure PgBouncer

## How a PostgreSQL Database Server works

A PostgreSQL Database Server accepts, processes requests, and returns results. The requests are received from client applications. The application code interacts with the database. The database searches, saves, manipulates data, and responds to the client.

PostgreSQL creates a separate process for each client connection that serves that connection. For example:

if there are ten client connections, PostgreSQL creates ten processes and allocates memory per connection.

if there are one hundred client connections, then one hundred PostgreSQL Server processes serve those one hundred client connections.

This type of architecture is called the Client-Server model. It is slow, inefficient, and doesn’t scale. It is limited to the number of client connections that the Database server can serve due to its resource limits of CPU and Memory.

## Improving efficiency with PgBouncer poolers

pgbouncer is a lightweight connection pooler for PostgreSQL. postgres db builds multiple connect, each connection consumes its memory. To improve this, and reduce the overhead of database connections for each request, a special utility called Database connection poolers can be used. For the PostgreSQL Database Server, one of the commonly used connection poolers is **PgBouncer**.

