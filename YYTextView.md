
##### 前言
源于项目重构，需要剔除无用的资源文件和减少第三方库的使用。在检查工程文件的时候发现**[UITextView-Placeholder](https://github.com/devxoul/UITextView-Placeholder)**有点多余( 注:**[YYText](https://github.com/ibireme/YYText)**已经存在).就想着用YYTextView来实现编辑签名。
######  YYTextView
> 创建视图
```
-(void)addOwnView{
self.textView = [YYTextView  new];
self.textView.placeholderText = @"请输入内容";
self.textView.placeholderFont = [UIFont systemFontOfSize:14];
[self.textView setBackgroundColor:[UIColor whiteColor]];
self.textView.font            = [UIFont systemFontOfSize:14];
self.textView.contentSize     = CGSizeMake(self.view.width, 200);
self.textView.contentInset    = UIEdgeInsetsMake(0, 10, 0, 10);
self.textView.frame = CGRectMake(0, 0, self.view.width, 200);
[self.view addSubview:self.textView];
}
```
> 添加监听事件
```
-(void)addObserver{

[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(textViewDidChangeNotification:) name:YYTextViewTextDidChangeNotification object:self.textView];
}
#pragma mark 关键方法
#pragma mark 方法来源   https://www.jianshu.com/p/2d1c06f2dfa4
-(void)textViewDidChangeNotification:(NSNotification*)obj{

YYTextView *textView = (YYTextView *)obj.object;
NSString *toBeString = textView.text;

//获取高亮部分
UITextRange *selectedRange = [textView markedTextRange];
UITextPosition *position = [textView positionFromPosition:selectedRange.start offset:0];
// 没有高亮选择的字，则对已输入的文字进行字数统计和限制
if (!position)
{
if (toBeString.length > MAX_STARWORDS_LENGTH)
{
NSRange rangeIndex = [toBeString rangeOfComposedCharacterSequenceAtIndex:MAX_STARWORDS_LENGTH];
if (rangeIndex.length == 1)
{
textView.text = [toBeString substringToIndex:MAX_STARWORDS_LENGTH];
}
else
{
NSRange rangeRange = [toBeString rangeOfComposedCharacterSequencesForRange:NSMakeRange(0, MAX_STARWORDS_LENGTH)];
textView.text = [toBeString substringWithRange:rangeRange];
}
}
}
}
```

> YYTextView监听方法校验修改

经过测试发现上面的方法根本行不通，测试才知道下面的selectedRange获取到值为空，想着如何才能获取selectedRange值的问题去看了一下源码。源码显示_markedTextRange是存在的,但是读取时却为空.想着用KVC获取一下试试，测试后发现完全可以。
```
UITextRange *selectedRange = [textView markedTextRange]; // 空值
```
```
YYTextRange *_selectedTextRange; /// nonnull
YYTextRange *_markedTextRange;
```
修改如下的代码为
```
YYTextView *textView = (YYTextView *)obj.object;
NSString *toBeString = textView.text;
//获取高亮部分
YYTextRange *selectedRange = [textView valueForKey:@"_markedTextRange"];
SEL selName = NSSelectorFromString(@"positionFromPosition:offset:");
YYTextPosition *position = ((YYTextPosition *(*)(id,SEL,id,int))objc_msgSend)(textView,selName,selectedRange.start,0);

// 没有高亮选择的字，则对已输入的文字进行字数统计和限制
if (!position)
{
if (toBeString.length > MAX_STARWORDS_LENGTH)
{
NSRange rangeIndex = [toBeString rangeOfComposedCharacterSequenceAtIndex:MAX_STARWORDS_LENGTH];
if (rangeIndex.length == 1)
{
textView.text = [toBeString substringToIndex:MAX_STARWORDS_LENGTH];
}
else
{
NSRange rangeRange = [toBeString rangeOfComposedCharacterSequencesForRange:NSMakeRange(0, MAX_STARWORDS_LENGTH)];
textView.text = [toBeString substringWithRange:rangeRange];
}
}
}
```
###### 感谢
小失: [iOS中UITextField的字数限制](https://www.jianshu.com/p/2d1c06f2dfa4)


