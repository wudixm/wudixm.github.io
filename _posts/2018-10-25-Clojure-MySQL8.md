## error 
### tx_isolation error

如果遇到如下error，网上说的是mysql 8 版本用的名字是`transactioin_isolation`，所以需要升级clojure java jdbc 和mysql java connector，当前测试用clojure jdbc 0.7.8 和mysql java connector 8.0.11 是可以成功插入的，一定要修改project.clj 文件中的，~/.lein/project.clj 中的可能也需要修改

Unknown system variable 'tx_isolation',

### MySQL 8.0 - Client does not support authentication protocol requested by server; consider upgrading MySQL client

如果是以上出错，则可能是用户没有密码导致，我的root 用户就没有密码，对用户添加了密码就好了
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password'
```
