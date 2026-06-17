---
title: misc-取证任务
date: 2025-10-17 14:37:23
tags: misc入门
---
# misc任务和取证任务

## 取证
> 解压取证任务得到三个文件
  打开FTK 然后添加证据项，选E01文件，然后再证据树中导出镜像文件
  ![quzheng-misc-1.png](/img/quzheng-misc-1.png)
  接下来用OSForensics打开镜像文件，查看文件系统,发现有bitlocker加密，那现在就想办法找到恢复密钥
  ![quzheng-misc-2.png](/img/quzheng-misc-2.png)
  那就用passware-kit来恢复密钥，选择磁盘破解，恢复加密密钥，然后我们有内存镜像,加密的镜像文件选择G.E01，内存镜像选择1.dmp。或者直接用diskgenius打开内存镜像，然后恢复文件然后开找就完事了.
  ![quzheng-misc-3.png](/img/quzheng-misc-3.png)
  ![quzheng-misc-4.png](/img/quzheng-misc-4.png)
  ![quzheng-misc-5.png](/img/quzheng-misc-5.png)
  ok,得到了bitlocker的恢复密钥，解密bitlocker加密的磁盘,得到四个文件，一个密码本，两个加密文件，还是用passware-kit来解密，选择文件解密，选择加密文件，选择密码本，解密就完事了.
  ![quzheng-misc-6.png](/img/quzheng-misc-6.png)
  ![quzheng-misc-7.png](/img/quzheng-misc-7.png)
  pptx文档输入密码，打开就是了
  ![quzheng-misc-8.png](/img/quzheng-misc-8.png)

## misc任务

### lsb隐写
> 接下来是lsb隐写，文件名已经提示了，那就用stegsolve来查看隐写信息
  ![misc-1.png](/img/misc-1.png)
  得到了一张二维码
  ![misc-2.png](/img/misc-2.png)
  用QR扫一下，得到flag,cumtctf{1sb_i4_s0_Ea4y}
  ![misc-3.png](/img/misc-3.png)

### 伪加密
> 粘一下知识点
- zip伪加密是在文件头的加密标志位做修改，进而再打开文件时识被别为加密压缩包
  一个 ZIP 文件由三个部分组成：
  压缩源文件数据区+压缩源文件目录区+压缩源文件目录结束标志

> 在这之前先粘贴一下伪加密的内容，那就用我在csdn上找的一个伪加密的内容，感谢大佬[伪加密和明文破解](https://blog.csdn.net/weixin_42739903/article/details/128569885)
那接下来就是用010打开文件，发现是伪加密，修改一下
  ![misc-4.png](/img/misc-4.png)
  解压一下，直接得到了flag
  ![misc-5.png](/img/misc-5.png)

### 流量分析
> 先解压一下文件，打开wireshark,先过滤一下，选个http看看，然后就是分组字节流找有没有带有flag的，然后对着找一下就找到了
  ![misc-6.png](/img/misc-6.png)

### 明文破解
> 粘一下知识点我们为zip压缩文件所设定的密码，首先被转换成3个32bit的key，所以可能的key的组合是2^96，这是个天文数字
如果用暴力穷举的方式是不太可能的，除非你的密码比较短或者有个厉害的字典。
压缩软件用这3个key加密所有包中的文件，也就是说，所有文件的key是一样的，如果我们能够找到这个key，就能解开所有的文件。
如果我们找到加密压缩包中的任意一个文件，这个文件和压缩包里的文件是一样的
我们把这个文件用同样的压缩软件同样的压缩方式进行无密码的压缩，得到的文件就是我们的Known plaintext（已知明文）。
用这个无密码的压缩包和有密码的压缩包进行比较，分析两个包中相同的那个文件，抽取出两个文件的不同点，就是那3个key了，如此就能得到key。
两个相同文件在压缩包中的字节数应该相差12个byte，就是那3个key了。虽然我们还是无法通过这个key还原出密码，
但是我们已经可以用这个key解开所有的文件，所以已经满足我们的要求了，毕竟对我们而言，得到解压后的文件比得到密码本身更重要。
> 到手发现就一个文件1.zip,啥也没有，暴力破解估计不可能，先010  看一下，发现是伪加密，修改一下，解压一下得到了1.png和2.zip
  ![misc-7.png](/img/misc-7.png)
  接下来用7.zip看看具体加密信息
  ![misc-8.png](/img/misc-8.png)
  好了我卡住了在这，我同时用kali里的bacrack和windows的archpr来破解，额，kali的一直爆不出来
  用010改过的1.zip当明文显示这个
  ![misc-9.png](/img/misc-9.png)
  分别用360压缩，rar压缩，wps压缩三种方法选择zip经典加密且设置密码为空的压缩包当明文显示这个
  ![misc-10.png](/img/misc-10.png)
  这当中我删除archpr重装了四五次，重做这道题5次以上，放弃了

### 古典密码
> XDAXFAAFFGFDAXGGFGFDDGGFAF
  问了chat，是古典五宫棋盘（Polybius / 5×5），观察发现这一串字符只有五个字母，所以是五宫棋盘密码，画出棋盘，每两个字母设置为行和列
  ![misc-11.png](/img/misc-11.png)
  得到密码WELCOMETOMISC
