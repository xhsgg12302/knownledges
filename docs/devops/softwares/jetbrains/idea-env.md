
+ ## 插件

    | - | - | - |
    --|--|--
    |Lombok|Maven Helper|Mybaits Log Plugin|
    |Translation|Find bugs|RestfulToolkit|
    |.ignore|Alibaba Java Coding Guidelines|jclasslib bytecode viewer|
    |Codota|Material Theme UI|SequenceDiagram|

+ ## 其他
    - ### 使用idea 插件反编译整个jar到文件夹
    `java -cp "D:\Program Files\JetBrains\IntelliJ IDEA 2018.2.8\plugins\java-decompiler\lib\java-decompiler.jar" org.jetbrains.java.decompiler.main.decompiler.ConsoleDecompiler`

    - ### 登录github授权总是失败
        !> 此次的原因是因为浏览器代理使用http:8889,而idea使用socks5:1089

    - ### idea gradle create separate module per source set in gradle.xml
        https://dkimitsa.github.io/2020/01/09/idea-disabling-separate-module-per-source-set/ </br>
        https://blog.jetbrains.com/idea/2019/05/intellij-idea-starts-the-2019-2-early-access-program/  
        add：
        ```xml
        <option name="resolveModulePerSourceSet" value="false" />
        ```
    
    - ### idea 激活 无限重置仓库(内置插件方式)
        http://idea.javatiku.cn/ </br>
        https://gitee.com/pengzhile/ide-eval-resetter.git

    - ### idea 激活方式2
        https://jetbra.in/s </br>
        https://3.jetbra.in/  
        下载相印的jar包，根据vmoptions文件夹配置，然后放入激活码。激活配置目录  在 application 下 content, clion.vmoptions

    - ### jrebel激活
        http://jrebel.qekang.com/ </br>
        https://www.jb51.net/article/201533.htm </br>
        http://jrebel-license.jiweichengzhu.com/

## References
*  https://stackoverflow.com/questions/28389006/how-to-decompile-to-java-files-intellij-idea