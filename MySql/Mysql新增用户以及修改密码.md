```mysql
drop user "snaker95"@"%";
create user "error_code_go_user"@"%" identified by "error_code_go_user.";
alter user 'error_code_go_user'@'%' identified with mysql_native_password by 'error_code_go_user.';
grant all privileges on *.* to 'error_code_go_user'@'%';
flush privileges;
```

