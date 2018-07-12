---
layout: post
title: Swift - 隐藏NavigationBar
date: 2016-09-21
tags: Swift
---


### 三种方法

1. 

    ```swift
    @available(iOS 8.0, *)
    func navHiddenForiOS8() {
        self.navigationController?.hidesBarsOnSwipe = true
    }
    ```
       
2. 

    ```swift
    func scrollViewDidScroll(scrollView: UIScrollView) {
        
        let pan = scrollView.panGestureRecognizer
        let velocity = pan.velocityInView(scrollView).y
        if velocity < -5 {
            self.navigationController?.setNavigationBarHidden(true, animated: true)
        } else if velocity > 5 {
            self.navigationController?.setNavigationBarHidden(false, animated: true)
        }
        
    }
    ```

3. 

    ```swift
    func scrollViewWillEndDragging(scrollView: UIScrollView, withVelocity velocity: CGPoint, targetContentOffset: UnsafeMutablePointer<CGPoint>) {
            if velocity.y > 0 {
                self.navigationController?.setNavigationBarHidden(true, animated: true)
            } else if velocity.y < 0 {
                self.navigationController?.setNavigationBarHidden(false, animated: true)
            }
        }   
    ```

    
