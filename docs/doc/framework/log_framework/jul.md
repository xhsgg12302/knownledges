## JUL(java util logging)

* ### 配置模板

    > [?] `vim $(dirname $(which java))/../jre/lib/logging.properties`
    <br>`vim ``dirname $(which java)``/../jre/lib/logging.properties`
    ```properties
    ############################################################
    #   Default Logging Configuration File
    #
    # You can use a different file by specifying a filename
    # with the java.util.logging.config.file system property.
    # For example java -Djava.util.logging.config.file=myfile
    ############################################################

    ############################################################
    #   Global properties
    ############################################################

    # "handlers" specifies a comma separated list of log Handler
    # classes.  These handlers will be installed during VM startup.
    # Note that these classes must be on the system classpath.
    # By default we only configure a ConsoleHandler, which will only
    # show messages at the INFO and above levels.
    handlers= java.util.logging.ConsoleHandler

    # To also add the FileHandler, use the following line instead.
    #handlers= java.util.logging.FileHandler, java.util.logging.ConsoleHandler

    # Default global logging level.
    # This specifies which kinds of events are logged across
    # all loggers.  For any given facility this global level
    # can be overriden by a facility specific level
    # Note that the ConsoleHandler also has a separate level
    # setting to limit messages printed to the console.
    .level= INFO

    ############################################################
    # Handler specific properties.
    # Describes specific configuration info for Handlers.
    ############################################################

    # default file output is in user's home directory.
    java.util.logging.FileHandler.pattern = %h/java%u.log
    java.util.logging.FileHandler.limit = 50000
    java.util.logging.FileHandler.count = 1
    java.util.logging.FileHandler.formatter = java.util.logging.XMLFormatter

    # Limit the message that are printed on the console to INFO and above.
    java.util.logging.ConsoleHandler.level = INFO
    java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter

    # Example to customize the SimpleFormatter output format
    # to print one-line log message like this:
    #     <level>: <log message> [<date/time>]
    #
    # java.util.logging.SimpleFormatter.format=%4$s: %5$s [%1$tc]%n

    ############################################################
    # Facility specific properties.
    # Provides extra control for each logger.
    ############################################################
    com.xyz.foo.level = SEVERE
    ```

* ### 使用

## Reference
* https://bilibili.com/video/BV13A41137Tc?p=6&vd_source=550a4dc4b2a914c0681a14307bbe8cbe
* https://note.youdao.com/s/7retQ6yb