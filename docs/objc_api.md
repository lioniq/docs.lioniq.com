# Objective-C

## 项目引入

在 `ViewController` 中引入插件生成 `LIQWebview` 的实例就可以引入商城、及购物车界面。
(使用前请到官网后台申请生成 `APP` 的 `APP_KEY` 和 `APP_SECRET` 帐号权限)

````
#import "ShopViewController.h"

@interface ShopViewController ()

@end

@implementation ShopViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 添加到本view
    self.webview = [[LIQWebview alloc] initWithFrame:[self.webviewPlaceholder.frame];
    self.view.addSubview(self.webview);

    // 设置代理
    self.webview.delegate = self;

    // 实现商城
    [self.webview reloadShop:@"我的_APP_KEY", secret: @"我的_APP_SECRET", userId: @"消费者_USER_ID"];
}

@end
````
