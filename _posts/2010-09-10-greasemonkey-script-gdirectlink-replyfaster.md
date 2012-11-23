--- 
date: 2010-09-10 22:06:16 +08:00
layout: post
title: !binary |
    5YaZ5LqG5Lik5LiqR3JlYXNlTW9ua2V56ISa5pys

wordpress_id: 407
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDA3

---
今天一时兴起，写了两个GreaseMonkey小脚本，稍有点用处。
1、Google Direct Link
这个主要是用来应付在Google搜索时，有时候直接点击链接会显示链接断开之类的无法访问的问题，如果是直接访问网址就能正常打开，原因是直接点击链接时在A中有一个onmousedown事件，这个事件把链接追加到Google的访问统计的页面再转发，为什么通过Google的某些页面就无法访问，你知道的，为了解决这样的问题，用一个小脚本给每个链接追加一个直接网址链接就可以了。

// ==UserScript==
// @name           Google DirectLink
// @namespace      http://www.google.com/
// @include        http://www.google.com/search*
// ==/UserScript==
var con = document.getElementById("ires");
var lis = con.getElementsByTagName('li');
//alert(lis.length);
for(i = 0, c = lis.length; i < c; i++) {
	var h3 = lis[i].getElementsByTagName('h3')[0];
	var url = h3.getElementsByTagName('a')[0].href;
	//var btn = document.createElement("INPUT");
	//btn.type = "button";
	//btn.value = "Direct Link";
	//btn.addEventListener("click",function(){window.open(url,'');},false);
	var btn = document.createElement("A");
	btn.setAttribute("href", url);
	btn.setAttribute("target", "_blank");
	btn.textContent = "Direct Link";
	btn.style.cssText = "display:inline-block;padding:3px;margin-left:20px;background:#0000ff;color:#fff;border:0;cursor:pointer;text-decoration:none;";
	lis[i].insertBefore(btn, h3.nextSibling.nextSibling);
}


2、为了偷懒而写的快速回复贴子的脚本，公司内部多数的交流和工作安排都是通过内部论坛进行的，有时候不用在里面回复多复杂的内容，所以弄个小脚本进行快速回复，本脚本只适用于Discuz6.0，等有了好的方法，我会考虑写一个通用的脚本提供给大家用。

// ==UserScript==
// @name           Reply Faster
// @namespace      http://www.tobycn.org/
// @description    快速回帖
// @include        http://worklog.gcemedia.co.nz/viewthread.php*
// ==/UserScript==
var _rf_tid = 'subject';
var _rf_cid = 'message';
var _rf_bid = 'postsubmit';
var _rf_ot = null;
var _rf_oc = null;
var _rf_ob = null;
var _rf_can = false;
function reply(title, content) {
	if(!_rf_can) return false;
	if(title!='') _rf_ot.value = title;
	_rf_oc.value = content;
	_rf_ob.click();
}
function $(id) {
	if(typeof id =='object') return id;
	try {
	switch(id.substring(0,1)) {
		case "#":
			__obj = document.getElementById(id.substr(1));
			break;
		case ".":
			break;
		default:
			__didx = id.indexOf('.');
			if(__didx==-1) __obj = document.getElementsByName(id);
			else {
				__tag = id.substring(0, __didx);
				__class = id.substr(__didx);
				__tobj = document.getElementsByTagName(__tag);
				__obj = [];
				for(i = 0, c = __tobj.length; i < c; i++) {
					if(__tobj.className==__class) __obj[__obj.length] = __tobj;
				}
			}
			break;
	}
	return __obj;
	}catch(e) {return false;}
}
function init() {
	_rf_ot = $(_rf_tid);
	_rf_oc = $('#'+_rf_cid);
	_rf_ob = $('#'+_rf_bid);
	if(!_rf_ot||!_rf_oc||!_rf_ob) return false;
	_rf_can = true;
	return true;
}
function creatediv() {
	var _div = document.createElement('DIV');
	_div.setAttribute("id", "_rf_show");
	_div.style.cssText = "padding:10px; position:fixed; bottom:300px; right:30px; border:1px #0000ff solid;background:#eee;";
	var _ht = document.createElement('h3');
	_ht.style.cssText = "margin-top:-5px;color:#0000ff;text-align:center;";
	_ht.textContent = "快速回复";
	_div.appendChild(_ht);
	var _select = document.createElement('SELECT');
	_select.setAttribute('id', '_rf_select');
	_select.options[_select.options.length] = new Option('请选择','请选择要回复的内容');
	_select.options[_select.options.length] = new Option('好的，我知道了，马上处理','好的，我知道了，马上处理');
	_select.options[_select.options.length] = new Option('已经完成了，请查收邮件','已经完成了，请查收邮件');
	_select.options[_select.options.length] = new Option('感谢楼主，顶一个','感谢楼主，顶一个');
	_select.options[_select.options.length] = new Option('哈哈哈，哈哈哈，^_*','哈哈哈，哈哈哈，^_*');
	_select.addEventListener('change', function() {reply('',$('#_rf_select').value)},false);
	_div.appendChild(_select);
	document.body.insertBefore(_div, $('#footer'));
}
if(init()) creatediv();
else alert('Init Failed');
