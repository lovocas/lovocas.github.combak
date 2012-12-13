---
layout: post
title: C语言判断文本文件的编码
tags:
- c
- encoding
- 编程资料
type: post
---
不同编码的文本，是根据文本的前两个字节来定义其编码格式的。定义如下：

>ANSI：　　　　　　　　无格式定义；
>Unicode： 　　　　　　前两个字节为FFFE；
>Unicode big endian：　前两字节为FEFF；
>UTF-8：　 　　　　　　前两字节为EFBB；
-- 以上是在网上找到的内容，没再仔细找，比如GB2312/GB18030等，不过原理应该是一样的吧

以下是我写的一段测试用的代码

{% highlight c %}
#include <stdio.h>
int main(int argc, char *argv[])
{
    FILE *f;
    unsigned char code[2];
    short rlen = 0;
    if (argc <= 1) {
        printf(" Usage: %s filename [ filename2 [...]]", argv[0]); return -1;
    }
    for ( ; --argc > 0; ) {
        f = fopen(*++argv, "rb");
        if (f) {
            rlen = fread(code, 1, 2, f);
            if(rlen == 2) {
                if (code[0]==0xEF && code[1]==0xBB) {
                    printf("%s encode UTF-8\n", *argv);
                }else if(code[0]==0xFF && code[1]==0xFE) {
                    printf("%s encode Unicode\n", *argv);
                }else if(code[0]==0xFE && code[1]==0xFF) {
                    printf("%s encode Unicode Big Endlian\n", *argv);
                }else {
                    printf("%s encode ASCII\n", *argv);
                }
            }else {
                printf("%s unknow err\n", *argv);
            }
            fclose(f);
        }else
            printf("%s not exists\n", *argv);
    }
}
{% endhighlight %}