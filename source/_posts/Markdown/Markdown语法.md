---
title: Markdown语法
date: 2021-05-01 00:00:00
author: 神奇的荣荣
summary: ""
tags: Markdown
categories: Markdown
---

# Markdown标题

## 使用 = 和 - 表示一级和二级标题

= 和 - 标记语法格式如下：
```
我展示的是一级标题
=================

我展示的是二级标题
---------------
```

<!-- more -->

## 使用 # 号标记

使用 # 号可表示 1-6 级标题，一级标题对应一个 # 号，二级标题对应两个 # 号，以此类推。

```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

# Markdown 段落格式

## 段落

Markdown 段落没有特殊的格式，直接编写文字就好，段落的换行是使用两个以上空格加上回车。  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/36A89BDA-A062-4D66-A41B-0EBEE7891AB9.jpg)

当然也可以在段落后面使用一个空行来表示重新开始一个段落。  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/3F254936-778E-417A-BEF2-467116A55D00.jpg)

## 字体

Markdown 可以使用以下几种字体：
```
*斜体文本*
_斜体文本_
**粗体文本**
__粗体文本__
***粗斜体文本***
___粗斜体文本___
```

显示效果如下所示：  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/md3.gif)

## 分隔线

你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线：
```
***

* * *

*****

- - -

----------
```

显示效果如下所示：  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/3F46EAA9-DADE-48FD-99AA-DF7BEBFAA4FA.jpg)

## 删除线

如果段落上的文字要添加删除线，只需要在文字的两端加上两个波浪线 ~~ 即可，实例如下：
```
RUNOOB.COM
GOOGLE.COM
~~BAIDU.COM~~
```

显示效果如下所示：  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/B5270A31-15D0-410B-AE1D-B9655B8F331C.jpg)

## 下划线

下划线可以通过 HTML 的 <u> 标签来实现：
```
<u>带下划线文本</u>
```

显示效果如下所示：  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/05A27273-B66D-43DE-A3DB-0D32FF024093.jpg)

## 脚注

脚注是对文本的补充说明。  
Markdown 脚注的格式如下:
```
[^要注明的文本]
```

以下实例演示了脚注的用法：
```
创建脚注格式类似这样 [^RUNOOB]。

[^RUNOOB]: 菜鸟教程 -- 学的不仅是技术，更是梦想！！！
```

演示效果如下：  
![效果图](https://www.runoob.com/wp-content/uploads/2019/03/md5.gif)

# Markdown列表

Markdown 支持有序列表和无序列表。

## 无序列表

无序列表使用星号(*)、加号(+)或是减号(-)作为列表标记，这些标记后面要添加一个空格，然后再填写内容：
```
* 第一项
* 第二项
* 第三项

+ 第一项
+ 第二项
+ 第三项

- 第一项
- 第二项
- 第三项
```

显示结果如下：  
![无序列表图](https://www.runoob.com/wp-content/uploads/2019/03/89446A8E-6D83-4666-AACC-980145D5F070.jpg)

## 有序列表

有序列表使用数字并加上 . 号来表示，如：
```
1. 第一项
2. 第二项
3. 第三项
```

![有序列表图](https://www.runoob.com/wp-content/uploads/2019/03/560384BB-2B00-41D5-ACF2-18972F7F2775.jpg)

## 列表嵌套

列表嵌套只需在子列表中的选项前面添加四个空格即可：
```
1. 第一项：
    - 第一项嵌套的第一个元素
    - 第一项嵌套的第二个元素
2. 第二项：
    - 第二项嵌套的第一个元素
    - 第二项嵌套的第二个元素
```

显示结果如下：
![列表嵌套图](https://www.runoob.com/wp-content/uploads/2019/03/8ED795DA-F124-4E70-BA71-57CD9CF958A4.jpg)

# Markdown区块

## 区块

Markdown 区块引用是在段落开头使用 > 符号 ，然后后面紧跟一个空格符号：
```
> 区块引用
> 菜鸟教程
> 学的不仅是技术更是梦想
```

显示结果如下：
![区块图](https://www.runoob.com/wp-content/uploads/2019/03/DFE1124E-BC38-4C12-B7AC-053E560D4C9C.jpg)

另外区块是可以嵌套的，一个 > 符号是最外层，两个 > 符号是第一层嵌套，以此类推：
```
> 最外层
> > 第一层嵌套
> > > 第二层嵌套
```
显示结果如下：
![区块嵌套图](https://www.runoob.com/wp-content/uploads/2019/03/AA0A4A6A-33A7-48C7-971F-73FFC8FE85B0.jpg)

## 区块中使用列表

区块中使用列表实例如下：
```
> 区块中使用列表
> 1. 第一项
> 2. 第二项
> + 第一项
> + 第二项
> + 第三项
```
显示结果如下：
![区块中使用列表图](https://www.runoob.com/wp-content/uploads/2019/03/E3BF6399-6483-4C7A-8502-AE75E8D66C96.jpg)

## 列表中使用区块

如果要在列表项目内放进区块，那么就需要在 > 前添加四个空格的缩进。

列表中使用区块实例如下：
```
* 第一项
    > 菜鸟教程
    > 学的不仅是技术更是梦想
* 第二项
```
显示结果如下：
![列表中使用区块图](https://www.runoob.com/wp-content/uploads/2019/03/1B894FB4-53AC-4E2D-BA30-F4AE4DFA8B97.jpg)

# Markdown图片

Markdown 图片语法格式如下：
```
![alt 属性文本](图片地址)

![alt 属性文本](图片地址 "可选标题")
```
* 开头一个感叹号 !
* 接着一个方括号，里面放上图片的替代文字
* 接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上选择性的 'title' 属性的文字。

使用实例：
```
![RUNOOB 图标](http://static.runoob.com/images/runoob-logo.png)

![RUNOOB 图标](http://static.runoob.com/images/runoob-logo.png "RUNOOB")
```
显示结果如下：  
![图片使用Demo图](https://www.runoob.com/wp-content/uploads/2019/03/A042DF30-C232-46F3-8436-7D6C35351BBD.jpg)


Markdown 还没有办法指定图片的高度与宽度，如果你需要的话，你可以使用普通的 &lt;img&gt; 标签。
```html
<img src="http://static.runoob.com/images/runoob-logo.png" width="50%">
```
显示结果如下：
![图片img使用Demo图](https://www.runoob.com/wp-content/uploads/2019/03/55F2A67D-F4BD-4960-AC55-DC690A415878.jpg)

# Markdown代码

## 代码片段

如果是段落上的一个函数或片段的代码可以用反引号把它包起来（`），例如：
```
`printf()` 函数
```
显示结果如下：
![片段代码图](https://www.runoob.com/wp-content/uploads/2019/03/C928FDA3-E0A7-4AFF-AB2A-B3AF44F93DF9.jpg)

## 代码区块

代码区块使用 4 个空格或者一个制表符（Tab 键）。（没效果）

你也可以用 ``` 包裹一段代码，并指定一种语言（也可以不指定）：
```java
public void test1() {
    System.out.println("你好~");
}
```
显示效果如下：
![代码区块2](https://www.runoob.com/wp-content/uploads/2019/03/88F52386-2F98-4D7E-8935-E43BECA6D868.jpg)

# Markdoun链接

链接使用语法如下：
```
[链接名称](链接地址)

or

<链接地址>
```

列如：
```
(https://www.baidu.com/)

[百度一下](https://www.baidu.com/)
```
显示效果：
// TODO

# Markdoun表格

# Markdoun高级技巧