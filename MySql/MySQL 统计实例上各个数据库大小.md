```mysql
select TABLE_SCHEMA as data_name,concat(round(sum(DATA_LENGTH/1024/1024/1024), 2), 'GB') as data_size from tables group by TABLE_SCHEMA;
```

