# mysqldump免密备份方法

<font size=5 color="red">注意：我是root用户登录的 mysql版本5.7</font>

```shell
cd ~ #家目录
vim .my.cnf # 在家目录添加该文件
# 将以下内容写入文件
[mysqldump]
user=root #用户
password=XXXXXX #密码替换掉
# 保存后 不需要重启msyql

chmod 600 .my.cnf # 为了提高安全性，修改文件权限

mysqldump -u root --databases db > db.sql # 备份语句中一定不能使用-p选项，否则还是需要输入密码的

# 当服务器有多个数据库实例时需要指定套接字 -S /path/to/mysql.sock
```

然后写一个shell脚本文件

```shell
databases=("xxx" "先从小程序") # 数据库名称 数组
for database in ${databases[@]};do
   date=$(date "+%Y-%m-%d %H:%M:%S")
   echo "开始备份 ${database} 数据库 时间 ${date}" >> /home/backup/sql/sql.log
   mysqldump -u root $database > /home/backup/sql/${database}.sql
   date=$(date "+%Y-%m-%d %H:%M:%S")
   echo "结束备份 ${database} 数据库 时间 ${date}" >> /home/backup/sql/sql.log
done
```

加入 linux crontab 里面

`01 01 */5 * * sh xxx.sh #每五天执行一次脚本`