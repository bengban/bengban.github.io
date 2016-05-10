---
layout: post
title: Swift - TableViewCell 自适应高度
date: 2016-05-10
tags: Swift
---


本文是学习开源HYBSnapkitAutoCellHeight的总结博文。

## 本文可能涉及的关键知识点

* swift 动态添加属性
* SnapKit 的使用

## 实现思路
	1.使用自动布局`立即`得到位置和大小(调用layoutIfNeeded方法)
	2.指定某一视图为最后一个视图，作为参考，根据该视图的frame计算cell高度。
	  同时，提供一个属性用于设置最后一个视图与cell底部的间隔。

		# 通过Runtime,对Extension添加属性 #
		```swift
			public var hyb_lastViewInCell: UIView? {
			  get {
			    let lastView = objc_getAssociatedObject(self, &__hyb_lastViewInCellKey);
			    return lastView as? UIView
			  }
			  
			  set {
			    objc_setAssociatedObject(self,
			      &__hyb_lastViewInCellKey,
			      newValue,
			      .OBJC_ASSOCIATION_RETAIN_NONATOMIC);
			  }
			}
			 
			public var hyb_bottomOffsetToCell: CGFloat? {
			  get {
			    let offset = objc_getAssociatedObject(self, &__hyb_bottomOffsetToCell);
			    return offset as? CGFloat
			  }
			  
			  set {
			    objc_setAssociatedObject(self,
			      &__hyb_bottomOffsetToCell,
			      newValue,
			      .OBJC_ASSOCIATION_ASSIGN);
			  }
			}
		```

## DEMO链接
* [CoderJackyHuang/HYBSnapkitAutoCellHeight](https://github.com/CoderJackyHuang/HYBSnapkitAutoCellHeight.git)

## 参考链接
* HYBSnapkitAutoCellHeight开发：[开源HYBSnapkitAutoCellHeight自动算行高](http://www.henishuo.com/snapkit-auto-cell-height/)
* UIView的layoutIfNeeded解释：[UIView的setNeedsLayout, layoutIfNeeded 和 layoutSubviews 方法之间的关系解释](http://blog.csdn.net/meegomeego/article/details/39890385)
* Objective-C Runtime 成员变量与属性：[Objective-C Runtime 运行时之二：成员变量与属性 / ](http://blog.jobbole.com/79570/)[动态添加属性的方法——关联(e.g. 向Category添加属性)](http://www.cnblogs.com/wengzilin/p/4331685.html?utm_source=tuicool)[ / ASSOCIATED OBJECT](http://swifter.tips/associated-object/)