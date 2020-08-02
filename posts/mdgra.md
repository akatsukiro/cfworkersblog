应该挺全得了吧
<!--more-->
全文引用自：
基础语法：
[菜鸟链接](https://www.runoob.com/markdown)  
基础语法及小部分数学公式：
[简书链接](https://www.jianshu.com/p/191d1e21f7ed)
几乎所有数学公式：
[CMD MarkDown](https://www.zybuluo.com/codeep/note/163962#cmd-markdown-%E5%85%AC%E5%BC%8F%E6%8C%87%E5%AF%BC%E6%89%8B%E5%86%8C)

> 文章迁移来迁移去，少了不少。

## Title  
### code:

    # 这是一级标题
    ## 这是二级标题
    ### 这是三级标题
    #### 这是四级标题
    ##### 这是五级标题
    ###### 这是六级标题
   
    我展示的是一级标题
    =================
    
    我展示的是二级标题
    -----------------

### e.g.
   
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题

我展示的是一级标题
=================

我展示的是二级标题
-----------------


## Font  
### code:

    **这是加粗的文字**
    *这是倾斜的文字*`
    ***这是斜体加粗的文字***
    ~~这是加删除线的文字~~
    _斜体文本_
    __粗体文本__
    ___粗斜体文本___
    ++下划线文本++
    ==黄色荧光==
    
### e.g.

**这是加粗的文字**  
*这是倾斜的文字*`  
***这是斜体加粗的文字***  
~~这是加删除线的文字~~  
_斜体文本_  
__粗体文本__  
___粗斜体文本___  
++下划线文本++

==黄色荧光==

## Block/Quote

### code:

    >这是引用的内容  
    >>这是引用的内容  
    >>>>>>
    >>>>>>>>>>这是引用的内容  
    
### e.g.
>这是引用的内容  
>>这是引用的内容  
>>>>>>that's it
>>>>>>>>>>这是引用的内容  

## Dividing line

### code:

    ---
    ----
    ***
    *****
    
### e.g.

---
----
***
*****

## Image

### code:

    ![图片alt](图片地址 ''图片title'')
    
    图片alt就是显示在图片下面的文字，相当于对图片内容的解释。
    图片title是图片的标题，当鼠标移到图片上时显示的内容。title可加可不加
    
##### Image同样可以使用变量
    这个链接用 1 作为网址变量 [RUNOOB][1].
    然后在文档的结尾为变量赋值（网址）
    
    [1]: http://static.runoob.com/images/runoob-logo.png
    
##### Markdown还没有办法指定图片的高度与宽度，如果需要的话，可以使用普通的 `<img>` 标签。

    <img src="http://static.runoob.com/images/runoob-logo.png" width="50%">

### e.g.

![RUNOOB 图标](http://static.runoob.com/images/runoob-logo.png)

![RUNOOB 图标](http://static.runoob.com/images/runoob-logo.png "RUNOOB")

##### Image同样可以使用变量
##### 这个链接用 1 作为网址变量
[RUNOOB][1] 
然后在文档的结尾为变量赋值（网址）
    
[1]: http://static.runoob.com/images/runoob-logo.png

##### img标签
<img src="http://static.runoob.com/images/runoob-logo.png" width="50%">

## List
### code:  
##### a  

    * 第一项
    * 第二项
    * 第三项
    
    + 第一项
    + 第二项
    + 第三项
    
   