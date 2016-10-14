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
    self.webview = [[LIQWebview alloc] initWithFrame:[self webviewPlaceholder].frame];
    [self.view addSubview: self.webview];

    // 设置代理
    self.webview.delegate = self;

    // 实现商城
    [self.webview reloadShop: @"我的_APP_KEY", secret: @"我的_APP_SECRET", userId: @"消费者_USER_ID"];
}

@end
````

## 插件 API

- `- (void)initWithFrame:(CGRect *)frame`
    + 初始化 `LIQWebview` 实例
    + params

        param | class | notes
        ------|-------|---------
        `frame` | `CGRect` | 实例出现的 `frame`

    + returns: 无

- `- (void)reloadShop:(NSString*)key (NSString*)secret (NSString*)userId`
    + 载入商城页面
    + params
        
        param | class | notes
        ------|-------|--------
        `key` | `NSString` | 应用 APP_KEY, 从后台获取
        `secret` | `NSString` | 应用 APP_SECRET, 从后台获取
        `userId` | `NSString?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

    + returns: 无


- `- (void)reloadCart:(NSString*)key (NSString*)secret (NSString*)userId`
    + 载入购物车页面
    + params
        
        param | class | notes
        ------|-------|--------
        `key` | `NSString` | 应用 APP_KEY, 从后台获取
        `secret` | `NSString` | 应用 APP_SECRET, 从后台获取
        `userId` | `NSString?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆
        
    + returns: 无

- `- (void)reloadShopForUser: (NSString*)userId`
    + 刷新商城页面，用来更新消费者用户登陆状态
    + params
        
        param | class | notes
        ------|-------|-------
        `userId` | `NSString?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆
        
    + returns: 无
    
- `- (void)reloadCartForUser:(NSString*)userId`
    + 刷新购物车页面，用来更新消费者用户登陆状态
    + params
        
        param | class | notes
        ------|--------|--------
        `userId` | `NSString?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆
        
    + returns: 无

#### 代理方式

所有插件事件通过 `LIQWebviewDelegate` 代理方式监听：

- `- (void)webviewDidMain`
    + 插件载入主页路由事件，列如: 从产品页面返回商城首页而显示 `NavigationBar` 及 `TabBar`. 商城、购物车两个主页都会响应这个事件.

- `- (void)webviewDidItemDetail`
    + 插件载入产品详情路由事件，列如: 从商城页面到详情页面时隐藏 `NavigationBar` 及 `TabBar`.  商城、购物车两个主页都会响应这个事件.

- `- (void)webviewDidLoadItem:(NSDictionary<NSString *,id> *)itemData`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `itemData` | `NSDictionary<NSString *,id>` | 产品信息 JSON, 请查看 [ItemData](json_objects.md)

- `- (void)webviewDidAddToCart:(NSDictionary<NSString *,id> *)cartItemData`
    + 加入购物车事件, 若用户未登陆时可以在这个事件显示登陆／注册界面。也可以用来响应 UI 效果给用户, 列如: TabBar 购物车图标上添加 badge 显示有产品加入了购物车. 
    + params
        
        param | class | notes
        ------|-------|---------
        `cartItemData` | `NSDictionary<NSString *,id>` | 购物车列表项信息 JSON, 请查看 [CartItemData](json_objects.md)

- `- (void)webviewDidCheckout`
    + 插件载入结算路由事件

- `- (void)webviewDidOrder:(NSDictionary<NSString *,id> *)orderData`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `orderData` | `NSDictionary<NSString *,id>` | 订单信息 JSON, 请查看 [OrderData](json_objects.md)


