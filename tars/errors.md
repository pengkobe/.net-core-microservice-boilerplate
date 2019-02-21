# ERRORS

排查方式

```bash
pm2 logs tars-node-web
```

## table db_tars_web.t_patch_task' doesn't exist

- https://github.com/TarsCloud/TarsWeb/issues/6

```bash
cd TarsWeb/sql 
mysql -uroot -p${mysql_passwork} db_tars_web < db_tars_web.sql
```

## Web 系统报系统内部错误

原因之一

- https://github.com/TarsCloud/Tars/issues/337
