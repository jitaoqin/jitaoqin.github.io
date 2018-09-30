---
title: jQuery手册
date: 2018-09-19 10:49:11
categories: 前端
tags:
	-jQuery
---


# 3 jQuery 选择器

## 3.1 基本选择器
| 选择器 		| 实例	| 功能	|
|:--------------|-------|---------------|
|  * 		| $("*") | 返回所有元素	|
|  #id    	|$("#tt")| 返回id=“tt”的元素	|
| .class	|$(".blue")| 返回class=“blue”的元素	|
| element	|$("p")| 返回所有p元素的	|
| .class,.class	|$(".blue","red")| 返回class="blue"或class="red"的元素	|
| elemet.class	|$("p.blue")| 返回p元素且class="blue"的元素	|

## 3.2 层级选择器

| 选择器              | 实例         | 功能                          |
| ------------------- | ------------ | ----------------------------- |
| ancestor descendant | $("div p")   | 返回div元素下的所有p元素      |
| parent   > child    | $("div > p") | 返回div元素标签为p的子元素    |
| prev   + next       | $("div + p") | 返回所有紧接在div后面的 p元素 |
| prev   ~ siblings   | $("div ~ p") | 返回div后面的同辈p元素。      |

##  3.3 基本过滤选择器

| 选择器         | 实例                 | 功能                                       |
| -------------- | -------------------- | ------------------------------------------ |
| :first         | $("p:first")         | 返回第一个p元素                            |
| :last          | $("p:last")          | 返回最后一个p元素                          |
| :even          | $("p:even")          | 返回所有index=偶数的p元素(index从0开始)    |
| :odd           | $("p:odd")           | 返回所有index=奇数的p元素                  |
|                |                      |                                            |
| :gt(index)     | $("ul li:gt(2)")     | 返回index大于2的所有li元素（index从0开始） |
| :eq(index)     | $("ul li:eq(2)")     | 返回index=2的的li元素                      |
| :lt(index)     | $("ul li:lt(2)")     | 返回index<2的li元素                        |
| :not(selector) | $("li:not(".blue")") | 返回所有class！="blue"的li元素             |

##  3.4  内容过滤选择器

| 选择器            | 实例                        | 功能                                 |
| ----------------- | --------------------------- | ------------------------------------ |
| :contains(*text*) | $("td:contains('I love U)") | 返回包含指定字符串的所有元素         |
| :empty            | $("td:empty")               | 返回无子（元素）节点的所有元素       |
| :parent           | $("td:parent")              | 返回含有子元素或者文本的元素         |
| :has(selector)    | $("div:has('p')")           | 返回所有包含有 p 元素在其内的div元素 |

## 3.5 可见性过滤选择器

| 选择器   | 实例           | 功能                 |
| -------- | -------------- | -------------------- |
| :hidden  | $("p:hidden")  | 返回所有隐藏的 p元素 |
| :visible | $("p:visible") | 返回所有可见的p元素  |

## 3.6 属性过滤选择器

| 选择器             | 实例                | 功能                                           |
| ------------------ | ------------------- | ---------------------------------------------- |
| [attribute]        | $("[name]")         | 返回所有带有 name属性的元素                    |
| [attribute=value]  | $("[name='test']")  | 返回所有 name 属性的值等于 "test" 的元素       |
| [attribute!=value] | $("[name!='test']") | 返回所有 name属性的值不等于 "test" 的元素      |
| [attribute$=value] | $("[name\$='test']"  | 返回所有 name属性的值包含以 "test" 结尾的元素 |

## 3.7 子元素过滤选择器

| 选择器                     | 实例                    | 功能                                                         |
| :------------------------- | ----------------------- | ------------------------------------------------------------ |
| :nth-child(index/even/odd) | $("ul li:nth-child(n)") | 返回父元素的第n个/偶数/奇数子元素，从1开始                   |
| :first-child               | $("ul li:first-child")  | 返回所有父元素ul的第一个子元素li<br><font color=0000ff>:first只匹配第一元素</font> |
| :last-child                | $("ul li:last-child")   | 返回所有父元素ul的最后一个子元素li                           |
| : only-child               | $("ul li:only-child")   | li是ul的唯一子元素则返回                                     |

## 3.8 表单属性过滤器

| 选择器    | 实例           | 功能                        |
| --------- | -------------- | --------------------------- |
| :enabled  | $(":enabled")  | 返回所有激活的 input 元素   |
| :disabled | $(":disabled") | 返回所有禁用的 input 元素   |
| :selected | $(":selected") | 返回所有被选取的 input 元素 |
| :checked  | $(":checked")  | 返回所有被选中的 input 元素 |

##  3.9 表单选择器

| 选择器    | 实例           | 功能                                            |
| --------- | -------------- | ----------------------------------------------- |
| :input    | $(":input")    | 返回所有 input, textarea, select 和 button 元素 |
| :text     | $(":text")     | 返回所有的单行文本框                            |
| :password | $(":password") | 返回所有密码框                                  |
| :radio    | $(":radio")    | 返回所有单选按钮                                |
| :checkbox | $(":checkbox") | 返回所有复选框                                  |
| :submit   | $(":submit")   | 返回所有提交按钮                                |
| :image    | $(":image")    | 返回所有图像就域                                |
| :reset    | $(":reset")    | 返回所有重置按钮                                |
| :button   | $(":button")   | 返回所有按钮（包括button)                       |
| :file     | $(":file")     | 返回所有文件域                                  |

