# windows添加catalina.out日志
**tomcat/bin路径下**
在start.bat中最末尾添加
```
将call "%EXECUTABLE%" start %CMD_LINE_ARGS%
替换为
call "%EXECUTABLE%" run %CMD_LINE_ARGS%
```
在catalina.bat最末尾中添加
```
在下面4个%ACTION%后面添加>>%CATALINA_HOME%/logs/catalina.%date:~0,4%-%date:~5,2%-%date:~8,2%.out
```