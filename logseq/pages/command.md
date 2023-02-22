- ## 清除孤立包
  ```
   sudo pacman -Rns $(sudo pacman -Qdtq)
  ```
- ## find pid
  ```
  pidof firefox
  pgrep firefox
  ps aux | grep firefox
  ```
- ##
- ## kill pid
  :LOGBOOK:
  CLOCK: [2022-09-04 Sun 21:59:01]--[2022-09-04 Sun 21:59:02] =>  00:00:01
  :END:
  ```
  kill -SIGKILL pid
  kill 13591
  pkill wechat
  ```
- ## tar
- 1. 使用tar压缩文件
  ```
  tar -zcvf test.tar.gz ./test/  
  ```
- 该命令表示压缩当前文件夹下的文件夹test，压缩后缀名为test.tar.gz
- 如果不需要压缩成gz，只需要后缀为tar格式的，那么输入如下命令：
- tar -cvf test.tar ./test/
-
- 2. 使用tar解压文件
- tar -xzvf test.tar.gz
- 该命令表示把后缀为.tar.gz的文件解压到当前文件夹下。
- 如果压缩文件的后缀是.tar，没有gz，则使用命令:
- tar -xvf test.tar
- ————————————————
  版权声明：本文为CSDN博主「易大飞」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
  原文链接：https://blog.csdn.net/stf1065716904/article/details/81909320
- ## 查看端口被占用
  ```
   sudo netstat -nplt
  ```
- ## 输出重定向
  ```
  > /dev/null 2>&1
  ```