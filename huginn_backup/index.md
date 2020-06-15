# Huginn（Docker）数据备份与恢复

   {{< image src="/images/Huginn/Huginn.png" >}} 
   
    简单介绍使用Docker运行的Huginn数据的备份与恢复

<!--more-->

### 备份数据

使用命令在容器中生成备份：

```bash
$ docker exec -it huginn mysqldump --single-transaction --opt -u root -ppassword huginn_production > huginn_backupfile.sql
```

或直接拷贝数据到容器外：

```bash
$ docker exec -it  huginn mysqldump -u root -ppassword huginn_production > /root/test_db.sql
```

### 恢复数据

首先复制文件到daocker中：

```bash
$ docker cp /home/xxxx/huginn_backupfile.sql huginn: /app/xxxxx/huginn_backupfile.sql
```

然后进入docker：

```bash
$ docker exec -ti huginn bash
```

接下来恢复数据库:

```bash
$ mysql -u root -ppassword huginn_production < huginn_backupfile.sql
```
