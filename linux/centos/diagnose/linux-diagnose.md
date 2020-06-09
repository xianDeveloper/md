# 服务器诊断

## top

各个参数含义以及操作交互命令

### memory

- swap

  ```bash
  # 显示所有占用swap分区进程
  for i in `cd /proc;ls |grep "^[0-9]"|awk ' $0 >100'` ;do awk '/Swap:/{a=a+$2}END{print '"$i"',a/1024"M"}' /proc/$i/smaps ;done |sort -k2nr
  # 显示前10占用swap分区
  for i in $( cd /proc;ls |grep "^[0-9]"|awk ' $0 >100') ;do awk '/Swap:/{a=a+$2}END{print '"$i"',a/1024"M"}' /proc/$i/smaps 2>/dev/null ; done | sort -k2nr | head -10
  
  ```

  

## cpu

