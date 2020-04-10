## 证书过期怎么办 acme 更新证书

`acme --renew -d example.com --force --ecc` 这里我把acme 做成命令 放入~/.bashrc 文件里 指向 acme.sh

<font size=4 color="red">如果报错说端口被占用 把对应的端口关闭即可 开着 nginx 的 有可能 nginx 占用了</font>

`nginx -s stop` 关掉

`lsof -i :80` 查看占用端口 kill 掉相应进程

**自动更新**

```shell
# shell 脚本文件
certificate=("example.com") # 数组
# 关闭占用的80端口
nginx -s stop
date=$(date +%Y-%m-%d)
echo "证书开始更新时间 ${date}" >> ~/certificate.log
for url in ${certificate[@]};do
# 更新证书
    /acme.sh --renew -d $url --force --ecc >> ~/certificate.log
done
date=$(date +%Y-%m-%d)
echo "证书结束更新时间 ${date}" >> ~/certificate.log
# 开启80端口
nginx
```

```shell
crontab -e
01 01 01 * * sh ~/x.sh # 每月一日凌晨一点一分 执行脚本文件
```