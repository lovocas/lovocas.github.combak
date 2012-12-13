---
layout: post
title: PHP函数fopen与file_get_content用法
tags:
- context
- file_get_content
- fopen
- php
type: post
---
一直都是简单的使用fopen和file_get_content的函数，今天刚知道，这两个函数还能这么用，看来我火星了。
用file_get_contents或者fopen、file、readfile等函数读取url的时候，会创建一个名为$http_response_header的变量来保存http响应的报头，使用fopen等函数打开的数据流信息可以用stream_get_meta_data来获取。
php5中新增的参数context使这些函数更加灵活，通过它我们可以定制http请求，甚至post数据。
{% highlight php %}
<?php
$html = file_get_contents('http://www.example.com/');
print_r($http_response_header); 
// or 
$fp = fopen('http://www.example.com/', 'r'); 
print_r(stream_get_meta_data($fp)); 
fclose($fp); 
?>
{% endhighlight %}

{% highlight php %}
<?php
$data = array ('foo' =&gt; 'bar');
$data = http_build_query($data);

$opts = array (
    'http' => array (
        'method' => 'POST',
        'header'=> "Content-type: application/x-www-form-urlencoded\r\n" .
                   "Content-Length: " . strlen($data) . "\r\n",
        'content' => $data
    ),
);

$context = stream_context_create($opts);
$html = file_get_contents('http://www.example.com', false, $context);

echo $html;
?>;
{% endhighlight %}
参考：
 * http://cn.php.net/manual/zh/function.file-get-contents.php
 * http://cn.php.net/manual/en/function.stream-context-create.php
 * http://cn.php.net/manual/zh/wrappers.http.php