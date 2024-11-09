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

PgBouncer is a middleware process responsible for managing a connection pool(s) to the Database(s). Clients connect to PgBouncer in the same way they would connect to the Database server. The Database server accepts the connections from PgBouncer as if they were regular clients.

By efficiently managing the connection pool(s), PgBouncer can handle a large number of incoming client connections and redirect them to a much smaller number of actual connections by using the pool(s). When the client application sends hundreds of requests to the database, PgBouncer serves as an intermediary. It distributes the requests among several dozen connections to the Database server and creates queues when necessary using settings like listen_backlog and query_wait_timeout.

This approach makes the management of Database connections more efficient, ensuring that all connection requests are handled effectively.

## Advantages of this approach

First, the application continues to work even if the number of requests dramatically increases. This is because each request does not create a separate process for the database. Instead, the requests are made to PgBouncer, which translates them into a small number of connections to the database by managing connection pool(s). Second, the application works faster, as time is saved by not creating a separate dedicated process in the database per each request.

## How to install pgbouncer using helm and verify pgbouncer is successfully connected with postgress db

-  First clone the helm chart availabe in the repo to your local system, and connect the GKE/EKS cluster and use the below command for installing the pgbouncer helm chart in kubernetes cluster.

        helm install <revision-no> <chart-name>
        or
        helm install pgbouncer pgbouncer/  # for the first time you need to install this, after that on every changes on values.yaml you need to apply the changes using "helm upgrade" command.

- once it deploy, go inside the pgbouncer pod and verify the postgres cred that pgbouncer uses to connect with postgres db. you have set this in pgbouncer helm chart.

          cat /etc/pgbouncer/pgbouncer.in   and see database section init.

-  now connect with pgbouncer inside the pgbouncer pod and verify that pgbouncer is connected with postgres db or not..
  
           psql -h localhost -p 5432 -U pg -d pgbouncer         # you can set the user for pgbouncer in pgbouncer helm chart values.yaml under config section but -d should be pgbouncer, i have not add this in my pgbouncer helm cart under values.yaml.

- once get connect with pgbouncer, use below command to verify the pgbouncer connection with postgres database.

        SHOW SERVERS;    # It will give you the detail information, like which pgbouncer pod having which ip is connected with postgres database. which have what ip..
        SHOW POOLS;     # it will give you the detail information of client how is connected with pgbouncer.
    
        cl_active: it will give you the infomration that how mny client connect with pgbouncer
    
        sv_active: it will give you the information like pgbouncer is successfully connected with postgres db..
    

  
    
