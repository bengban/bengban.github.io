---
layout: post
title: Swift - TableViewCell 自适应高度
date: 2016-05-10
tags: Swift
---


本文是学习开源HYBSnapkitAutoCellHeight的总结博文。

### 本文可能涉及的关键知识点

* swift 动态添加属性
* SnapKit 的使用

### 实现思路及实现源码
1. 使用自动布局立即得到位置和大小(调用layoutIfNeeded方法)
	
2. 指定某一视图为最后一个视图，作为参考，根据该视图的frame计算cell高度。
	同时，提供一个属性用于设置最后一个视图与cell底部的间隔。

	```swift
	//通过Runtime,对Extension添加属性 
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
	    objc_setAssociatedObject(self,&__hyb_bottomOffsetToCell, newValue, 
	    	.OBJC_ASSOCIATION_ASSIGN);
	  }
	}
	```

3. 自动计算行高-带缓存功能

	```swift
	/**
	 带缓存功能、自动计算行高
	 
	 - parameter tableView:                 给哪个tableview缓存
	 - parameter config:                        在回调中配置数据
	 - parameter cache:                         指定缓存key/stateKey/tableview
	 - parameter stateKey:                  stateKey表示状态key
	 - parameter shouldUpdate       是否要更新指定stateKey中缓存高度，若为YES,不管有没有缓存 ，都会重新计算
	 
	 - returns: 行高
	 */
	public class func hyb_cellHeight(forTableView tableView: UITableView,
	  config: ((cell: UITableViewCell) -> Void)?,
	  updateCacheIfNeeded cache: ((Void) -> (key: String, stateKey: String, shouldUpdate: Bool))?) -> CGFloat {
	    
	    if let cacheBlock = cache {
	      let keyGroup = cacheBlock()
	      let key = keyGroup.key
	      let stateKey = keyGroup.stateKey
	      let shouldUpdate = keyGroup.shouldUpdate;
	      
	      if shouldUpdate == false {
	        if  let cacheDict = tableView.hyb_cacheHeightDictionary {
	          // 状态高度缓存
	          if let stateDict = cacheDict[key] as? NSMutableDictionary {
	            if let height = stateDict[stateKey] as? NSNumber {
	              if height.intValue != 0 {
	                return CGFloat(height.floatValue)
	              }
	            }
	          }
	        }
	      }
	    }
	    
	    let className = String(UTF8String: class_getName(self));
	    var cell = tableView.hyb_cacheCellDictionary?.objectForKey(className!) as? UITableViewCell;
	    
	    if cell == nil {
	      cell = self.init(style: .Default, reuseIdentifier: nil)
	      tableView.hyb_cacheCellDictionary?.setObject(cell!, forKey: className!);
	    }
	    
	    if let block = config {
	      block(cell: cell!);
	    }
	    
	    return cell!.hyb_calculateCellHeight(forTableView: tableView, updateCacheIfNeeded: cache)
	}

	private func hyb_calculateCellHeight(forTableView tableView: UITableView,
	  updateCacheIfNeeded cache: ((Void) -> (key: String, stateKey: String, shouldUpdate: Bool))?) -> CGFloat {
	    assert(self.hyb_lastViewInCell != nil, "hyb_lastViewInCell property can't be nil")
	    
	    layoutIfNeeded()
	    
	    var height = self.hyb_lastViewInCell!.frame.origin.y + self.hyb_lastViewInCell!.frame.size.height;
	    height += self.hyb_bottomOffsetToCell ?? 0.0
	    
	    if let cacheBlock = cache {
	      let keyGroup = cacheBlock()
	      let key = keyGroup.key
	      let stateKey = keyGroup.stateKey
	      
	      if let cacheDict = tableView.hyb_cacheHeightDictionary {
	        // 状态高度缓存
	        let stateDict = cacheDict[key] as? NSMutableDictionary
	        
	        if stateDict != nil {
	          stateDict?[stateKey] = NSNumber(float: Float(height))
	        } else {
	          cacheDict[key] = NSMutableDictionary(object: NSNumber(float: Float(height)), forKey: stateKey)
	        }
	      }
	    }
	    return height
	}
	```
	
### 使用方法

	####

### DEMO链接
* [CoderJackyHuang/HYBSnapkitAutoCellHeight](https://github.com/CoderJackyHuang/HYBSnapkitAutoCellHeight.git)

### 参考链接
* HYBSnapkitAutoCellHeight开发：[开源HYBSnapkitAutoCellHeight自动算行高](http://www.henishuo.com/snapkit-auto-cell-height/)
* UIView的layoutIfNeeded解释：[UIView的setNeedsLayout, layoutIfNeeded 和 layoutSubviews 方法之间的关系解释](http://blog.csdn.net/meegomeego/article/details/39890385)
* Objective-C Runtime 成员变量与属性：[Objective-C Runtime 运行时之二：成员变量与属性 / ](http://blog.jobbole.com/79570/)[动态添加属性的方法——关联(e.g. 向Category添加属性)](http://www.cnblogs.com/wengzilin/p/4331685.html?utm_source=tuicool)[ / ASSOCIATED OBJECT](http://swifter.tips/associated-object/)