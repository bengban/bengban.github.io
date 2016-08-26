---
layout: post
title: Swift - layout相关
date: 2016-08-26
tags: Swift
---


### 本文可能涉及的关键知识点

* layout相关的方法
  ``` swift
    public func setNeedsLayout()
    
    public func layoutIfNeeded()
    
    public func layoutSubviews()
  ```

### 方法解释
1. layoutSubviews
    * 对subviews重新布局
    * 方法调用先于drawRect
    * init初始化不会触发layoutSubviews，但是是用initWithFrame 进行初始化时，当rect的值不为CGRectZero时，也会触发
    * addSubview会触发layoutSubviews
    * 设置view的Frame会触发layoutSubviews，当然前提是frame的值设置前后发生了变化
    * 滚动一个UIScrollView会触发layoutSubviews
    * 旋转Screen会触发父UIView上的layoutSubviews事件
    * 改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件
    
    注：Frame 改变的时候就会调用layoutSubiews 
       
2. setNeedsLayout
    * 标记为需要重新布局，异步调用layoutIfNeeded刷新布局，不立即刷新，但layoutSubviews一定会被调用
    * UIKit会判断该receiver是否需要layout，有需要刷新的标记，立即调用layoutSubviews进行布局（如果没有标记，不会调用layoutSubviews）

3. layoutIfNeeded
    * layoutIfNeeded遍历的不是superview链，应该是subviews链
    * UIKit会判断该receiver是否需要layout，有需要刷新的标记，立即调用layoutSubviews进行布局（如果没有标记，不会调用layoutSubviews）
    * 如果要立即刷新，要先调用[view setNeedsLayout]，把标记设为需要布局，然后马上调用[view layoutIfNeeded]，实现布局
    * 在视图第一次显示之前，标记总是“需要刷新”的，可以直接调用[view layoutIfNeeded]
  


### 参考链接
* layoutSubviews总结：[layoutSubviews总结](http://blog.csdn.net/bsplover/article/details/7977944)
* UIView的layoutIfNeeded解释：[UIView的setNeedsLayout, layoutIfNeeded 和 layoutSubviews 方法之间的关系解释](http://blog.csdn.net/meegomeego/article/details/39890385)