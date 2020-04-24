# command

1. 常用高效命令

   | ctrl+f                 |  光标向右移动，等于方向键右              |
   | 快捷键                 | 说明           |
   | ---------------------- | -------------- |
   | tab                    | 命令，路径补全 |
   | 移动快捷键             |                |
   | ctrl+a                 |  光标回到命令行首              |
   | ctrl+e                 |  光标回到命令行尾              |
   | ctrl+f                 |  光标向右移动，等于方向键右              |
   | ctrl+b                 |  光标向左移动，等于方向键左              |
   | 剪贴，粘贴，清除快捷键 |                |
   | ctrl+insert                 |  复制命令行内容	              |
   | shift+insert                 |  粘贴命令行内容              |
   | ctrl+k                 | 剪切（删除）光标处到行尾的字符* |
   | ctrl+u                 | 剪切（删除）光标处到行首的字符* |
   | ctrl+w                 | 剪切（删除）光标前的一个单词 |
   | ctrl+y                 | 粘贴ctrl+u,ctrl+k,ctrl+w删除的文本 |
   | ctrl+c                 | 中断 |
   | ctrl+h                 | 删除光标所在处的前一个字符 |
   | 重复执行命令行快捷键                |                |
   | ctrl+d                 | 退出当前shell命令行 |
   | ctrl+r                 | 搜索命令行使用过的历史命令记录 |
   | ctrl+g                 | 从执行ctrl+r的搜索历史命令模式退出 |
   | Esc+.                 | 获取上一条命令的最后的部分（空格分隔） |
   | 控制快捷键                 |                |
   | ctrl+i                 | 清除屏幕所有的内容，并且在屏幕最上面开始一个新行，等同于clear命令 |
   | ctrl+s                 | 锁定中断，无法输入内容 |
   | ctrl+q                 | 解锁执行ctrl+s的锁定状态 |
   | ctrl+z                 | 暂停执行在终端运行的任务 |
   | !号开头快捷键                 |                |
   | !!                 | 执行上一条命令 |
   | !pw                 | 执行最近以pw开头的命令 |
   | !pw:p                 | 打印最近以pw开头的命令不执行 |
   | !num                 | 执行历史命令列表的第num条命令 |
   | !$                 | 上一条命令的最后一个参数，相当于esc+. |
   | ESC相关                |                |
   | Esc+.                  | 获取上一条命令的最后的部分（空格分隔） |
   | Esc+b                  | 移动到当前单词的开头 |
   | Esc+f                  | 移动到当前单词的词尾 |
   | Esc+t                  | 颠倒光标所在处以及其相邻单词的位置 |

   

2. 查看文件编码

   - vim中 用 

     ```bash
     :set fileencoding
     ```

   - 安装enca命令

     ```bash
     sudu yum install -y enca
     enca file
     ```

3. 查找文件

   1. whereis or find

4. 常用安装

   ​	

   ```bash
   yum install -y lrzsz  #上传下载
   yum install -y vim
   yum install -y 
   ```

5. 防火墙操作

6. 管道操作

7. 压缩 复制

   ``` bash
   scp root@107.172.27.254:/home/test.txt .   //下载文件
   
   scp test.txt root@107.172.27.254:/home  //上传文件
   
   scp -r root@107.172.27.254:/home/test .  //下载目录
   
   scp -r test root@107.172.27.254:/home   //上传目录
   ```

8. ll命令

   ```bash
   ll -h #按照人类习惯 kb mb gb展示
   ```

9. vi,cat,vim

   美化显示

   ```bash
   cat file | python -mjson.tool 
   ```

   

10. find命令

    find命令用于查找文件。

    按文件名字查找：

    ```bash
    wang@wang:~/workpalce/python$ sudo find / -name "create.txt"
    /home/wang/workpalce/python/create.txt
    ```

    按文件大小查找：

    ```bash
    wang@wang:~/workpalce/python$ sudo find / -size +100M
    /usr/lib/i386-linux-gnu/libOxideQtCore.so.0
    /sys/devices/pci0000:00/0000:00:0f.0/resource1_wc
    /sys/devices/pci0000:00/0000:00:0f.0/resource1
    /proc/kcore
    ```

    按文件权限查找：

    ```bash
    wang@wang:~/workpalce/python$ find . -perm 664
    ./create.txt
    ```

     按文件类型查找：

    ```bash
    wang@wang:~/workpalce/python$ find . -type f
    ./1.txt
    ./3.txt
    ./2.txt
    ./n.tar
    ```

    类型参数列表： f 普通文件 l 符号连接 d 目录 c 字符设备 b 块设备 s 套接字 p Fifo

    按文件时间查找：

    Linux文件系统每个文件都有三种时间戳： 访问时间（-atime/天，-amin/分钟）：用户最近一次访问时间。

    ​                                                                 修改时间（-mtime/天，-mmin/分钟）：文件最后一次修改时间。

    ​                                                                 变化时间（-ctime/天，-cmin/分钟）：文件数据元（例如权限等）最后一次修改时间。

    ```bash
    wang@wang:~/workpalce/python$ find . -atime -7   # 七天内访问过的文件
    .
    ./1.txt
    ./3.txt
    ./2.txt
    ./n.tar
    wang@wang:~/workpalce/python$ find . -atime 7    # 恰好在七天前被访问过的文件
    wang@wang:~/workpalce/python$ find . -atime +7   # 超过在七天内被访问过的文件
    ```

    二、借助-exec选项与其他命令结合使用

    ```bash
    wang@wang:~/workpalce/python$ find dir -name "*.txt" -exec cp {} . \;
    ```

