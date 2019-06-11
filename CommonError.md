记录自己开发遇到的问题
> 解决办法:提高Deployment Target 
* cannot create __weak reference because the current deployment target does not support weak reference

> _cachedSystemAnimationFence requires the main thread
 *  需要在主线程操作UI
 ```
 NSInternalInconsistencyException
 accessing _cachedSystemAnimationFence requires the main thread
 CoreFoundation    ___exceptionPreprocess + 228
 libobjc.A.dylib    objc_exception_throw + 56
 CoreFoundation    +[NSException raise:format:]
 Foundation    -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 112
 UIKit    -[UIApplication _cachedSystemAnimationFenceCreatingIfNecessary:] + 348
 UIKit    +[UIWindow _synchronizeDrawingWithPreCommitHandler:] + 68
 UIKit    -[UIInputViewAnimationStyle launchAnimation:afterStarted:completion:forHost:fromCurrentPosition:]
 ```
> 使用`DZNEmptyDataSet`时,`tableView `或者`collectionView `中的`DZNEmptyDataSetView`位置发生偏移
```
- (void)emptyDataSetDidAppear:(UIScrollView *)scrollView{
CGRect frame   = scrollView.frame;
frame.origin.y = 0;
UIView *view = objc_getAssociatedObject(scrollView,emptyDataSetView);
if (view) {
   view.frame  = frame;
 }
}
```

> CollectionView 数据量不足无法滚动问题

`@property(nonatomic) BOOL alwaysBounceVertical;` <br>
`@property(nonatomic) BOOL alwaysBounceHorizontal`

选择上面的属性,设置为YES,当数据量少的时候也可滚动


>   输入框空禁止点击键盘  `enablesReturnKeyAutomatically`

```
textField.enablesReturnKeyAutomatically = YES;
```
 输入框未输入任何文字键盘禁止输入,只需将`enablesReturnKeyAutomatically`设置为YES,默认是NO，需要注意的是微信检索功能也是利用这个属性做的。


