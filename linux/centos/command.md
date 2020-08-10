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
    
11. Linux查看文件及目录大小方法

    Linux用df命令查看挂入目录大小、使用比例、文件系统类型，但对文件却无能为力。但用du命令可以查看文件及目录的大小。下面以centos为例对比Linux系统df命令与du命令的区别：

    - **应用场景**

      1、执行文件操作，遇到No space left on device（磁盘空间不足）可通过查看磁盘文件大小了解总体布局。2、搬家转移文件之前查看文件大小，判断是否有足够空间可用。

    - **df命令用法**

      - **df -T**

        df -T查看挂载目录

      - **df -h**

        df -h查看挂载目录

        参数 -h 表示使用「Human-readable」的输出，使用 GB、MB 等人类易读的格式展示输出。与 -T（T大写）区别不显示文件系统类型，挂载目录大小更易读。

        所以二者结合使用df -Th查看挂载目录大小情况更佳

    - **du命令用法**

      - du常用参数

        ```bash
        -h：以人类易读的方式显示（GB、MB）
        -a：显示目录占用的磁盘空间大小，还要显示其下目录和文件占用磁盘空间的大小
        -s：显示目录占用的磁盘空间大小，不要显示其下子目录和文件占用的磁盘空间大小
        -c：显示几个目录或文件占用的磁盘空间大小，还要统计它们的总和
        --apparent-size：显示目录或文件自身的大小
        -l ：统计硬链接占用磁盘空间的大小
        -L：统计符号链接所指向的文件占用的磁盘空间大小
        ```

      - cd 定位目录

        ```bash
        du -sh 查看当前目录总容量。不单独列出各子项占用的容量。
        du -sh * 查看当前目录下的各文件及子目录的容量（各子项占用容量）。
        du -sh * | sort -n 统计当前目录下文件及子目录大小，并按 -n（大小）排序。
        ```

    - 

12. 释放内存

      **Linux****释放内存的命令：**

    ```
    sync
    echo 1 > /proc/sys/vm/drop_cachesdrop_caches
    ```

    ****的值可以是0-3之间的数字，代表不同的含义：**
    **0****：不释放（系统默认值）**
    **1****：释放页缓存**
    **2****：释放dentries和inodes3：释放所有缓存**

    **释放完内存后改回去让系统重新自动分配内存。**

    ```
    echo 0 >/proc/sys/vm/drop_caches
    free -m #看内存是否已经释放掉了。
    ```

    **如果我们需要释放所有缓存，就输入下面的命令：**

    ```
    echo 3 > /proc/sys/vm/drop_caches 
    ```

     		在Linux系统下，我们一般不需要去释放内存，因为系统已经将内存管理的很好。但是凡事也有例外，有的时候内存会被缓存占用掉，导致系统使用SWAP空间影响性能，

    ​		例如当你在linux下频繁存取文件后,物理内存会很快被用光,当程序结束后,内存不会被正常释放,而是一直作为caching。，此时就需 要执行释放内存（清理缓存）的操作了。Linux系统的缓存机制是相当先进的，他会针对dentry（用于VFS，加速文件路径名到inode的转换）、Buffer Cache（针对磁盘块的读写）和PageCache（针对文件inode的读写）进行缓存操作。但是在进行了大量文件操作之后，缓存会把内存资源基本用光。但实际上我们文件操作已经完成，这部分缓存已经用不到了。这个时候，我们难道只能眼睁睁的看着缓存把内存空间占据掉吗？所以，我们还是有必要来手动进行Linux下释放内存的操作，其实也就是 释放缓存的操作了。

    ​		/proc是一个虚拟文件系统,我们可以通过对它的读写操作做为与kernel实体间进行通信的一种手段.也就是说可以通过修改/proc中的文件,来对当前kernel的行为做出调整.那么我们可以通过调整/proc/sys/vm/drop_caches来释放内存。

    ​		要达到释放缓存的目的，我们首先需要了解下关键的配置文件/proc/sys/vm/drop_caches。这个文件中记录了缓存释放的参数，默认值为0，也就 是不释放缓存。一般复制了文件后,可用内存会变少，都被cached占用了，这是linux为了提高文件读取效率的做法：为了提高磁盘存取效率, Linux做了一些精心的设计, 除了对dentry进行缓存(用于VFS,加速文件路径名到inode的转换), 还采取了两种主要Cache方式：Buffer Cache和Page Cache。

    ​		前者针对磁盘块的读写，后者针对文件inode的读写。这些Cache有效缩短了 I/O系统调用(比如read,write,getdents)的时间。"释放内存前先使用sync命令做同步，以确保文件系统的完整性，将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件。否则在释放缓存的过程中，可能会丢失未保存的文件。

    ```bash
    [root@fcbu.com ~]# free -m
                 total       used       free     shared    buffers     cached
    Mem:          7979       7897         82          0         30       3918
    -/ buffers/cache:       3948       4031
    Swap:         4996        438       4558
    ```

    **第一行用全局角度描述系统使用的内存状况：**
    total 内存总数
    used 已经使用的内存数，一般情况这个值会比较大，因为这个值包括了cache 应用程序使用的内存
    free 空闲的内存数
    shared 多个进程共享的内存总额
    buffers 缓存，主要用于目录方面,inode值等（ls大目录可看到这个值增加）
    cached 缓存，用于已打开的文件

    **第二行描述应用程序的内存使用：**
    -buffers/cache 的内存数:used - buffers - cached
    buffers/cache 的内存数:free buffers cached
    前个值表示-buffers/cache 应用程序使用的内存大小，used减去缓存值
    后个值表示 buffers/cache 所有可供应用程序使用的内存大小，free加上缓存值

    **第三行表示swap的使用：**
    used 已使用
    free 未使用 

    可用的内存=free memory buffers cached。

    **为什么free这么小，是否关闭应用后内存没有释放？**
    		但实际上，我们都知道这是因为Linux对内存的管理与Windows不同，free小并不是说内存不够用了，应该看的是free的第二行最后一个值：-/ buffers/cache:       3948       4031 ，这才是系统可用的内存大小。

    ​		实际项目中的经验告诉我们，如果因为是应用有像内存泄露、溢出的问题，从swap的使用情况是可以比较快速可以判断的，但free上面反而比较难查看。我觉得既然核心是可以快速清空buffer或cache，但核心并没有这样做（默认值是0），我们不应该随便去改变它。

    ​		一般情况下，应用在系统上稳定运行了，free值也会保持在一个稳定值的，虽然看上去可能比较小。当发生内存不足、应用获取不到可用内存、OOM错 误等问题时，还是更应该去分析应用方面的原因，如用户量太大导致内存不足、发生应用内存溢出等情况，否则，清空buffer，强制腾出free的大小，可 能只是把问题给暂时屏蔽了，所以说一般情况下linux都不用经常手动释放内存。

    ​		向/proc/sys/vm/drop_caches中写入内容，会清理缓存。建议先执行sync（sync 命令将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件）。执行echo 1、2、3 至 /proc/sys/vm/drop_caches, 达到不同的清理目的

