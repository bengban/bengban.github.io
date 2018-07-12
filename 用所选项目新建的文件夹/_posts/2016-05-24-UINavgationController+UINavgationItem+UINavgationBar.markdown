---
layout: post
title: UINavigationController + UINavigationItem + UINavigationBar 学习
date: 2016-05-11
tags: 开源项目
---

由于要做自定义的UINavgationBar，总感觉出现各种各样的问题，就研究下他们三者的关系。

UINavigationController
1.UINavigationController是继承自UIViewController，管理着一个UIViewController栈。其中具有以下属性可供操作。

	``` swift
	public var topViewController: UIViewController? { get } // The top view controller on the stack.
	public var visibleViewController: UIViewController? { get } // Return modal view controller if it exists. Otherwise the top view controller.
	public var viewControllers: [UIViewController] // The current view controller stack.
	```
2.在VC(UIViewController)栈中，可以进行pop和push操作

	``` swift
	public func pushViewController(viewController: UIViewController, animated: Bool) // Uses a horizontal slide transition. Has no effect if the view controller is already in the stack.
    public func popViewControllerAnimated(animated: Bool) -> UIViewController? // Returns the popped controller.
    public func popToViewController(viewController: UIViewController, animated: Bool) -> [UIViewController]? // Pops view controllers until the one specified is on top. Returns the popped controllers.
    public func popToRootViewControllerAnimated(animated: Bool) -> [UIViewController]? // Pops until there's only a single view controller left on the stack. Returns the popped controllers.

	```
3.重头戏来了，下面是各种Bar属性和操作

	``` swift
	public var navigationBarHidden: Bool
    public func setNavigationBarHidden(hidden: Bool, animated: Bool) // Hide or show the navigation bar. If animated, it will transition vertically using UINavigationControllerHideShowBarDuration.
    public var navigationBar: UINavigationBar { get } // The navigation bar managed by the controller. Pushing, popping or setting navigation items on a managed navigation bar is not supported.
    

    public var toolbarHidden: Bool // Defaults to YES, i.e. hidden.
    public func setToolbarHidden(hidden: Bool, animated: Bool) // Hide or show the toolbar at the bottom of the screen. If animated, it will transition vertically using UINavigationControllerHideShowBarDuration.
    public var toolbar: UIToolbar! { get } // For use when presenting an action sheet.
	```
	问题来了，上面两个hidden 是什么鬼？ ---待研究---

4.其他的一些方法
	``` swift
	// 这个方法可以用于在自定义后，手势失效的问题中
	public var interactivePopGestureRecognizer: UIGestureRecognizer? { get }
	// @available(iOS 8.0, *)--- 8.0 懂的
	public func showViewController(vc: UIViewController, sender: AnyObject?) // Interpreted as pushViewController:animated:
	```

