* cp -a 对应于-pdf,完全形式的复制,权限,日期等
* mv
* rm
* basename去掉文件夹和后缀,只显示文件名
* dirname去掉最后一个组件文件名 


### 文件内容查阅
* cat 和 tac, 一前一后 concatenate 连接,连锁  -nbA 行号/显示全部包括断行,tab
* nl添加行号,提供较多的行号显示, -b a t 空行显示与否  -n 行号形式 -w 行号位数
* more 一页一页翻动
    * 空格: 向下翻一页
    * enter: 翻一行
    * /字串: 搜寻此字串
    * :f: 立即显示出档名以及当前显示的行数
    * q: 离开,并不再显示
    * b或ctrl+b: 往回翻页
* less
    * pagedown up
    * /字串: 向下搜索
    * ?字串: 向上搜索
* head 取出前面几行 -n num
* tail 取出后面几行 -n num
* od 非纯文本档 -t
    * a: 默认的字节
    * c: ASCII
    * 0fox 十进制/浮点数/八/十六
