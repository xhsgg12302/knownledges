## sed (Stream Editor):
!> 文本流编辑，是一个"非交互式的"面向字符流的编辑器。能同时处理多个文件多行内容，可以不对源文件改动，把整个文件输入到屏幕，可以把只匹配到模式的内容输出到屏幕上。还可以对源文件改动，但是不会在屏幕上返回结果。
    
### 一.语法格式：
```shell
sed [option] 'command' file
sed [option] -f 'sed script' file
```
    
### 二.常用选项：
```shell
[option]
    -n : 只打印模式匹配的行
    -e : 直接在命令模式上进行sed动作编辑，此为默认选项
    -f : 指定sed动作脚本文件
    -r : 支持扩展表达式
    -i : 直接修改文件内容（i.bak 修改前备份）
    
{script}
    p : 打印匹配行
    = : 显示文件行号
    a\: 在定位行号后附加新文本信息 
    i\: 在定位行号后插入新文本信息
    d : 删除定位行
    c\: 用新文本替换定位文本
    s : 使用替换模式替换相应模式
    q : 第一个模式匹配完成后退出或立即退出，并将模式内容输出
    i : 显示与八进制ASCII代码等价的控制符
    {}: 命令组，用分号隔开
    n : 从另一个文件中读文本下一行，并从下一条命令而不是第一个条命令开始对其处理
    N : 在数据流中添加一行以创建用于处理的多行组
    g : 将模式2粘贴到/pattern n/
    y : 传送字符，替换单个字符
    w file : 写文本到一个文件，类似输出重定向(>)
    r file : 读文本从一个文件，类似输入重定型(<)
```
    
* ### 三.文件处理：
    + #### 1. sed `查询`：
        - ##### 1）使用行号，可以是一个数字或者行号范围
        ```shell
        sed '2p' file   >打印文件中第二行，第二行会打印两遍，sed默认输出模式空间内容
        sed -n '2p' file   > 只打印匹配行
        sed -n '1,3p' file    > 1,2,3行
        sed -n '/second/p' file   >打印匹配second字符的行
        sed -n '/first/,4p' file  >打印匹配first的行到第四行，first所处的行在第四行之后的话，仅打印匹配first的行
        sed -n '2,/last/p' file   >打印第二行到第一次出现匹配last字符的行
        sed -n '/data/,/last/p' file  >打印匹配首次匹配到data字符的行到 （相对首次）出现last字符的行
        sed -n '1,4{=;p}' file   >打印1～4行行号及内容
        sed -n '1,2!{=;p}' file   >取反打印行号及内容
        sed -n '1,2!p' file   >取反打印内容

        # 相对首次（注释） > '/var/,/nologin/p' 下面的内容会一直匹配到行尾，处理每行时只要匹配到第一个pattern，后面的就不管了，所以一直没有碰见 nologin,直到文件末尾。
        cat > file <<EOF
        libstoragemgmt:/var/run/lsm:/sbin/nologin
        rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
        sshd:x:74:74:Privilege-separated
        SSH:/var/empty/sshd:/sbin/nologin
        eli:x:1000:1000::/home/eli:/bin/bash
        EOF
        ```
    
        - ##### 2）使用正则或扩展正则（-r）
        ```shell
        # 正则的介绍和一些愿字符暂时不记录了
        sed '5 q' file    >打印前5行,q(quit)
        sed -n '/r*t/p' file  
        sed -n '/.r.*/p' file
        sed -n '/o\{1,\}/p' file  # 打印o字符重复一次以上的

        sed -n '/^#/!p' file  # 过滤掉以#开头的行
        sed -n '/^#/!{/^$/!p}' file    # 取出不以#开头的并且打印非空行
        sed -e '/^#/d' -e '/^$/d' file    # 同上
        sed -n '1,/adm/p' file    # 打印第一行到首次出现adm的行
        sed -n '/adm/,6p' file    # 打印/adm/到第6行
        sed -n '/adm/,2p' file    # 相等行或错开行,只打印/adm/行
        ```

    + #### 2. sed `添加`:
    ```shell
    cat > myfile <<EOF
    hello world
    hello linux
    how are you 
    I am fine 
    thanks, and you
    EOF

    sed '/world/s/^/Li /' file    >在world字符行，行首添加Li
    sed 's/linux/jie &/' file     >在Linux字符行，Linux前面添加jie
    sed 's/linux/& jie/' file     >在Linux字符行，Linux后面添加jie
    sed '/you/s/$/ Li/' file  > 在you字符行，行尾添加Li
    sed '/you/s/\(.*\)/\1 Li/' file   > 分组引用，同上
    sed '/are/i nihao' file   >在are字符行上一行添加一行 nihao
    sed '/are/i\nihao' file   >同上
    sed '/are/i/nihao' file   >在are字符行上一行添加一行 /nihao
    sed '/are/a nihao' file   >在are字符行下一行添加一行 nihao
    sed '/are/a\nihao' file   >同上
    sed '/are/a\nihao\wo hen hao' file   > 如下：
    ...
    nihaowo hen hao
    ...
    sed '/are/a\nihao\n wo hen hao' file > 使用'\n'来换行，如下：
    ...
    nihao
        wo hen hao
    ...
    sed 's/^/Start /' file    >在每行开头添加Start
    sed 's/$/ End/' file  >在每行结尾添加End
    sed '1,3s/^/#/' file  >1~3行行首添加#号，一般用来注释某行
    sed 's/fine/very &' file  >将 'fine' 替换成 'very fine'

    #地址定界符，一般由三个组成，可以是/,$,#等其他特殊字符
    sed '/hello/s@End@tail@' file >在hello字符行，替换'End'为'tail'
    sed -e '/tail/s@tail@end@' file   >默认修改第一次出的'tail'
    sed -e '/tail/s@tail@end@g' file  >跟'g'表示替换所有字符
    sed -e '/tail/s@tail@end@2g' file >n+g表示替换第n次出现的字符
    ```
    
    + #### 3. sed `删除`:
    ```shell
    sed '/^#/d' file  >删除以#开头的行
    sed '/^#/!d' file >删除以非#开头的行
    sed '1d' file >删除文件第一行
    sed '$d' file >删除文件最后一行
    sed '2,4d' file   >删除文件制定行
    sed '/thanks/d' file  >删除thanks字符行
    sed '/\<you\>/d' file >删除you单词行
    sed -i -e '/sed/d' -e '/^#/d' file   >删除sed字符行和#开头的行
    sed '/End$/d' file    >删除以End结尾的行
    ```

    + #### 4. sed `编辑（替换）`:
    ```shell
    sed -i '/DEVICE/c\Ethernet' file  >以Ethernet替换整行内容
    sed -i 's/static/dhcp' file   >把static替换成dhcp
    sed -i '/IPADDR/s@22\.1@10\.12@' file > 22.1换成10.12
    sed -i '/connect/s#YES#NO#' file  > 把YES换成NO
    sed -i 's/bin/tom/2g' file    >第二次及以后出现的bin换成tom
    sed -i 's/bin/tom/2' file >仅第二次出现
    sed -i 's/bin/tom/2p' file    >多生产一行
    sed -i '/root/{s/bash/nologin/;s/0/1/g' file  >匹配root字符行，bash换成nologin，0换成1
    sed -i 's/root/(&)/g' file    >把'root'换成'(root)'
    sed -i '/ONBOOT/s/#//' file   >把#换成空,一般用于去掉注释
    ```

    + #### 5. sed `引用变量`:
    ```shell
    # 第一种当sed命令里面没有默认的变量时可以把单引号改成双引号
    # 第二种有的时候，自定义变量需要加单引号，脚本也需要加单引号
    name=wang
    sed -i "s/jie/$name/" file
    sed -i "$a $name" file   ❌
    sed -i '$a '$name'' file
    ```

    + #### 6. sed `高级使用`:
        - ##### 1),sed 操作文件的内容写到另外一个文件中
            ```shell
            cat > test >>EOF
            Ethernet  
            #BOOTPROTO="dhcp"  
            HWADDR="00:0C:29:90:79:78"  
            ONBOOT="yes"  
            IPADDR=172.16.10.12  
            NETMASK=255.255.0.0
            EOF
            
            sed -i 's/IPADDR/ip/w ip.txt' test
            > ip.txt
            ip=172.16.10.12
            ```
            
        - ##### 2),sed 读取一个文件到正在sed操作的文件中
            ```shell
            cat > myfile >>EOF
            
            hello world
            I am fine 
            how are you
            EOF
            
            > ip.txt
            
            ip=172.16.10.12
            
            sed -i '/ip/r myfile' ip.txt
            
            > ip.txt 
            ip=172.16.10.12
            hello world
            I am fine 
            how are you
            ```
        
    + #### 7. sed `经典例子`:
        - ##### 处理以下内容，并将域名取出计数排序
            ```shell
            cat > file <<EOF
            http://www.baidu.com/index.<a target="_blank" href="http://www.2cto.com/kf/qianduan/css/" class="keylink" style="border:none; padding:0px; margin:0px; color:rgb(51,51,51); text-decoration:none; font-size:14px">html</a>  
            http://www.baidu.com/1.html  
            http://post.baidu.com/index.html  
            http://mp3.baidu.com/index.html  
            http://www.baidu.com/3.html  
            http://post.baidu.com/2.html
            EOF
            
            cat file | sed -e 's/http:\/\///' -e 's/\/.*//' | sort | uniq -c | sort -rn
            ```

### 四.参考：
* [sed命令详解](https://www.cnblogs.com/ctaixw/p/5860221.html)