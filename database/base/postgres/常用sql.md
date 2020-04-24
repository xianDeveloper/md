# 常用sql

1. posgresql如何获取自增id的当前值或者下一个值？

2. postgresql 自增列

   ```sql
   select ROW_NUMBER () OVER (ORDER BY col_name ASC) AS ids,* from tableA;
   ```

   

