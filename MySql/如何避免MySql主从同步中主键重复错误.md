# 避免Mysql主从同步主键重复导致同步问题

（1）--slave_exec_mode 
可自动忽略，无记录和重复主键报错
默认值是 STRICT （严格模式）

set global slave_exec_mode='IDEMPOTENT';
stop slave；
start slave；

（2）--slave_skip_erros
非动态参数，跳过忽略指定sql 执行报错ERROR 号,
需要将改参数配置到my.cnf 文件中

--slave-skip-errors=1062,1053
--slave-skip-errors=all
--slave-skip-errors=ddl_exist_errors

