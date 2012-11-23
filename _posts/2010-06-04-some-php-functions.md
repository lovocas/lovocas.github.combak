--- 
date: 2010-06-04 18:03:05 +08:00
layout: post
title: !binary |
    44CQ6L2s44CR5LiA5Lqb6KKr5b+96KeG55qEIFBIUCDlh73mlbA=

wordpress_id: 357
wordpress_url: !binary |
    aHR0cDovL3d3dy5pc3BocC5vcmcvYi8/cD0zNTc=

---
真的是不用不知道，其实我们熟悉的 PHP 还有很多好东西没有发掘。看到这篇文章，当时就泪奔了好几回，重点推荐下，顺便我自己也做个整理。
sys_getloadavg()

这个函数返回当前系统的负载均值信息（当然 Windows 下不适用），详细文档可以翻阅 PHP 的相关文档。文档中有段示例代码，基本上也就能看出它的用途了。
[code lang="php"]
< ?php
$load = sys_getloadavg();
if ($load[0] > 80) {
    header('HTTP/1.1 503 Too busy, try again later');
    die('Server too busy. Please try again later.');
}
?>
[/code]
PS，如果“很不幸”得你的 PHP 环境中没有这个函数，可以考虑使用下面这段代码 via
[code lang="php"]
if (!function_exists('sys_getloadavg')) {
    function sys_getloadavg()
    {
        $loadavg_file = '/proc/loadavg';
        if (file_exists($loadavg_file)) {
            return explode(chr(32),file_get_contents($loadavg_file));
        }
        return array(0,0,0);
    }
}
[/code]
这一特性如果使用得当，能减轻服务器部分压力。
pack()

pack 对应的还有个函数为 unpack，用于压缩二进制串，文中的作者的示例非常清楚
[code lang="php"]
$pass_hash = pack("H*", md5("my-password"));
[/code]
如果你使用 PHP5，那么可以直接这样子
[code lang="php"]
$pass_hash = md5("my-password", true); // PHP 5+
[/code]
这样做的好处之一是能减少串存储空间（能节省多少呢？可能又会是另篇文章了）。

这里还有个示例代码可以 pack 数组 via
[code lang="php"]
< ?php
function pack_array($v,$a) {
 return call_user_func_array(pack,array_merge(array($v),(array)$a));
}
?>
[/code]
cal_days_in_month()

该函数可以直接返回指定月份中的天数，例如
[code lang="php"]
$days = cal_days_in_month(CAL_GREGORIAN, date("m"), date("Y")); // 31
[/code]
我敢保证，你自己实现过类似功能的函数 :^)
_()

呃，这的确也是个 PHP 函数（也有可能是最短的 PHP 内置函数）。_() 是它的“小名”，它的大名是 gettext()。

写过 Wordpress 皮肤的朋友会了解 __() 以及 _e() 这些函数，其实 PHP 早已经自带了相关的功能。
[code lang="php"]
// Set language to German
setlocale(LC_ALL, 'de_DE');

// Specify location of translation tables
bindtextdomain("myPHPApp", "./locale");

// Choose domain
textdomain("myPHPApp");

echo _("Have a nice day");
[/code]
利用 gettext 可以编写多语言的应用，现在您感兴趣的可能就是如何编写 locale 文件，这但已经不是此文涉及的重点，更多信息可以移步到这里。
get_browser()

坦白讲，见到这个函数我当时就彻底泪奔。有了这个函数，再也不用自己去分析 $_SERVER['HTTP_USER_AGENT'] 这个字符串了。

更多的信息可以参考这里。在使用此函数前，你可能需要个 browscap.ini 配置文件，相信你可以搞定的。
debug_print_backtrace()

以前查看函数调用堆栈，我会使用 xdebug 等的扩展，其实 PHP5 版本以后已经内置了相关的函数。

顺便再分享个“蛋疼”的小技巧，如果你记不住这个函数的名字，可以用这段代码同样能达到目的（看起来还是记住那个函数靠谱）：
[code lang="php"]
< ?php
$e = new Exception();
print_r(str_replace('/path/to/code/', '', $e->getTraceAsString()));
[/code]
natsort()

这个函数用于自然排序，这个大家可能都要用到。贴下相关的文档链接以及示例代码
[code lang="php"]
$items = array("100 apples", "5 apples", "110 apples", "55 apples");

// normal sorting:
sort($items);
print_r($items);
    # Outputs:
    # Array
    # (
    #     [0] => 100 apples
    #     [1] => 110 apples
    #     [2] => 5 apples
    #     [3] => 55 apples
    # )

natsort($items);
print_r($items);
    # Outputs:
    # Array
    # (
    #     [2] => 5 apples
    #     [3] => 55 apples
    #     [0] => 100 apples
    #     [1] => 110 apples
    # )
[/code]
有关自然排序的算法规则，可以参考这里的文档。
glob()

这个函数的功能同样让人感到泪奔，先不说功能直接上示例代码
[code lang="php"]
foreach (glob("*.php") as $file) {
    echo "$filen";
}
[/code]
相比你已经了解该函数的用途了，那么我们就可以有更多的“玩法”，例如就显示目录（via）：
[code lang="php"]
$dirs = array_filter(glob($path.'*'), 'is_dir');
[/code]
当然，文件递归你也可以考虑使用下 SPL 扩展。

补充 by 神仙：

glob 有个参数选项 GLOB_ONLYDIR 就可以只列目录

PHP Filter

如果你还在正则验证字符串，那么就真的“Out”了。自 PHP5.2 版本以后，内置了 PHP Fliter 模块用于专门验证 电子邮件、URL 等是否合法，示例代码：
[code lang="php"]
var_dump(filter_var('bob@example.com', FILTER_VALIDATE_EMAIL));
[/code]
由于是新生的模块，因此还有很多的陷阱，例如
[code lang="php"]
filter_var('abc', FILTER_VALIDATE_BOOLEAN); // bool(false)
filter_var('0', FILTER_VALIDATE_BOOLEAN);   // bool(false)
[/code]
但这不影响我们去尝试。有关 PHP Filter 的更多信息，相信能拎出来另外写篇文章了。

-- Split --

最后，感叹 PHP 其实是个历久弥新的工具，不小心我们就会悲剧性得重复造了只轮子。因此，时常看看 PHP 文档每次都会有新的收获。
转自：http://www.gracecode.com/archives/3013/
