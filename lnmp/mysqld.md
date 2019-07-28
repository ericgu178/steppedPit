## 解决MySQL中【Cannot load from mysql.proc. The table is probably corrupted。】的问题

```【错误过程】：MySQL从5.1升级至5.5后在调用存储过程时报出“Cannot load from mysql.proc. The table is probably corrupted。”```

```【造成原因】：MySQL升级完成后未对相关数据库执行升级.```

```【解决办法】：在命令行中执行mysql_upgrade -uroot -p 即可~```

mysql_upgrade -uroot -p  升级更新库字段
--------------------- 



# mysql 命令行导入数据库

```进入 mysql 选择数据库 source 绝对路径.sql```