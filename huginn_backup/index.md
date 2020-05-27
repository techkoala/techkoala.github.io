# Huginn（Docker）数据备份与恢复


### 备份数据

使用命令在容器中生成备份：
```shell
$ docker exec -it huginn mysqldump --single-transaction --opt -u root -ppassword huginn_production > huginn_backupfile.sql
```

或直接拷贝数据到容器外：
```shell
$ docker exec -it  huginn mysqldump -u root -ppassword huginn_production > /root/test_db.sql
```

### 恢复数据

首先复制文件到daocker中：
```shell
$ docker cp /home/wangjie/huginn_backupfile.sql huginn: /app/wjhuginn/huginn_backupfile.sql
```

然后进入docker：
```shell
$ docker exec -ti huginn bash
```

接下来恢复数据库:
```shell
$ mysql -u root -ppassword huginn_production < huginn_backupfile.sql
```
