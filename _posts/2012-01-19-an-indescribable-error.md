--- 
date: 2012-01-19 19:16:22 +08:00
layout: post
title: 一个因不注意细节引发的错误
categories: mysql
tags: [mysql, php]
---
昨天在开发过程中出现了一个意料之外的错误，经很长时间的调试才找到错误原因所在，唉～粗心要不得啊。

### 开发环境：
    Nginx+PHP-FPM+MySQL，连接MySQL使用的是MySQLi。

### 错误描述：
    在插入新数据时MySQL返回1064错误，1064是语法错误。

### 相关代码：
```
$row = array('userId'=>$uid, 'orderId'=>$oid, 'money' => $money,
      'change' => json_encode($change), 'createTime' => time());
if ($this->db->insert('recharge', $row)) {
    return true;
}else {
    return false;
}
```
这段代码会生成类似以下的SQL，并执行，出错1064错误
```
INSERT INTO recharge (userId, orderId, money, change, createTime)
 VALUES (2102103, '2012xxxxxxxxx', 200.00, '经json_encode编码的数组', 1326970280);
```

### 解决过程：

不知道大家能不能一眼看出这行SQL会在哪里出错，反正我是没能一眼看出来，所以，我没认为是在SQL中出错。
多次换不同位置中断调试，始终不行，后来干脆打印出sql，然后直接在数据库执行，还是提示相同的错误，这
让我很纠结啊，我在怀疑是不是有特殊符号没有转义，虽然我是用单引号包含的字符串，并且中间没有单引号，
但是我还是把特殊字符全转义了，可是还是相同提示。
    这时候我才注意到错误提示位置是从change开始的，这个时候才想起来change是MySQL中修改表的关键字，
把change外用反引号框起来：
```
$row = array('userId'=>$uid, 'orderId'=>$oid, 'money' => $money,
      '`change`' => json_encode($change), 'createTime' => time());
if ($this->db->insert('recharge', $row)) {
    return true;
}else {
    return false;
}
```
再执行，问题解决。
    这次出现这个问题让我注意到了MySQL在用反引号包含字段名和库表名的作法，在这之前我从来不用反引号
包围库表名或者字段名，之所以没出现问题是因为我有意识的避开了关键字。

### 教训：
    以后在写SQL时注意写上字段和库表名外面的反引号，类库的SQL构建函数中添加处理反引号的相关代码。当
然，以后在设计库表及字段名时还是要避开关键字。
