## 周项目问题及实现总结：

1. Danale SDK不能调用问题
![图片](http://cdn.cocimg.com/bbs/attachment/Fid_21/21_497367_ccebc3ef812d0d9.png)

解决方法：打包静态库的时候，有一个Link Frameworks Automatically设置，默认为YES，会自动链接框架。

![图片](https://github.com/iHTCboy/iGallery/raw/master/BlogImages/2017/10/Xcode-Settings.jpeg)


2. 去除Filed text用户输入时的蓝色边框问题(没找到快捷的办法，只能一个一个的去除焦点)。


>//点击的时候不显示蓝色外框

>self.focusRingType = NSFocusRingTypeNone; 

注：关闭焦点后，光标不再闪烁。


3. 文字垂直居中问题。


````object-c
>- (NSRect)adjustedFrameToVerticallyCenterText:(NSRect)frame {
>//super通常在单元格顶部绘制文本
>CGFloat fontSize = >self.font.boundingRectForFont.size.height;
>NSInteger offset = floor((NSHeight(frame) - >ceilf(fontSize))/2)-5;
>NSRect centeredRect = NSInsetRect(frame, 0, >offset);
>return centeredRect;
}

> - (void)editWithFrame:(NSRect)aRect inView:(NSView *)controlView
>editor:(NSText *)editor delegate:(id)delegate event:(NSEvent *)event {
[super editWithFrame:[self adjustedFrameToVerticallyCenterText:aRect]
>              inView:controlView editor:editor delegate:delegate event:event];
}

>- (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView
>editor:(NSText *)editor delegate:(id)delegate
> start:(NSInteger)start length:(NSInteger)length >{

>[super selectWithFrame:[self >adjustedFrameToVerticallyCenterText:aRect]
>                inView:controlView editor:editor >delegate:delegate
                 start:start length:length];
}

> - (void)drawInteriorWithFrame:(NSRect)frame inView:(NSView *)view {
[super drawInteriorWithFrame:
 [self adjustedFrameToVerticallyCenterText:frame] inView:view];
}
````
解释：首先这个是你自己新建一个类，继承自：NSTextFieldCell,然后把你xib或者sb或者自己纯代码写的继承自这个类，然后你就会实现居中。

注：这个距离一定要自己调整好，不然有一部分文字会被挡住。
>NSInteger offset = floor((NSHeight(frame) - ceilf(fontSize))/2)-5;



4. 明文暗文转换问题。


本次使用NSSecureTextField和NSTextField重叠实现可见输入的密码,前者展现的是不可见密码，后者展现的是可见密码，根据需要隐藏显示达到目的。


````object-c
IBOutlet NSSecureTextField *_secureTextField;
	IBOutlet NSTextField *_textField;
	IBOutlet NSButton *_chkBtn;
	-(IBAction)checkHideCode:(id)sender;
  //默认隐藏密码
-(void)awakeFromNib
{
	[_textField setHidden:YES];
	[_secureTextField setHidden:NO];
	[_secureTextField becomeFirstResponder];
	[_chkBtn setState:NSOnState];
}
 
-(IBAction)checkHideCode:(id)sender
{
	if ([_chkBtn state] == NSOnState) 
	{
		NSString *str = [_textField stringValue];
		[_textField setHidden:YES];
		[_secureTextField setHidden:NO];
		[_secureTextField setStringValue:str];
		[_secureTextField becomeFirstResponder];
	}
	else 
	{
		NSString *str = [_secureTextField stringValue];
		[_secureTextField setHidden:YES];
		[_textField setHidden:NO];
		[_textField setStringValue:str];
		[_textField becomeFirstResponder];
	}
>}
````
注：没找到更好的方法，太耗费时间和精力不建议用此方法。

5. 使用Webview加载网页。


(1)添加添加WebKit.framework.

![图片](https://img-blog.csdn.net/20161123152252486)

（2）关联Delegate.

![图片](https://img-blog.csdn.net/20161123152850035)

(3)创建WebView对象以及加载URL路径

````object-c
#import<WebKit/WebKit.h>

@interface ViewController : NSViewController`

@property (weak) IBOutlet WebView *myWebView;`

- (void)viewDidLoad {`

   [super viewDidLoad];
 
    NSString *urlString = @"要展示的网页地址";
    
    [[self.myWebView  mainFrame ] loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:urlString]]];
    }
  ````
  
  注：此时要想加载出网页还需要设置Xcode的网络请求权限。
  
  （1）xcode请求权限四步走（四步后就可以完成加载网页的实现）。
  
 ![图片](https://img-blog.csdn.net/20180425002207517)
 ![图片](https://img-blog.csdn.net/20180425002220935)
    
 





































































