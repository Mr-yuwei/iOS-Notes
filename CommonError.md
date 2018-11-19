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
