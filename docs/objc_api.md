# Objective-C

## 项目引入

在 `ViewController` 中引入插件生成 `LIQWebview` 的实例就可以引入商城、及购物车界面。
(使用前请到官网后台申请生成 `APP` 的 `APP_KEY` 和 `APP_SECRET` 帐号权限)

### 商城集成

````
#import "ShopViewController.h"

@interface ShopViewController () <LIQViewDelegate>

@end

@implementation ShopViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 针对 storyboard 7plus/6plus bug
    self.webviewPlaceholder.frame = self.view.frame;

    // 添加到本view
    self.liqview = [[LIQView alloc] initWithFrame:[self webviewPlaceholder].frame];
    [self.view addSubview: self.liqview];

    // LionIQ 单列管理对象
    LIQManager* liqManager = [LIQManager defaultManager];
    [liqManager setAppKeyWithAppKey:@"我的_APP_KEY", appSecret: @"我的_APP_SECRET"];

    // 设置代理
    self.liqview.delegate = self;

    // 实现商城
    [self.liqview reloadShop];

    // OPTIONAL 可选: 
    // 自动更新模版
    [liqManager getUpdates];

    // OPTIONAL 可选: 
    // 载入商城离线信息, 从后台下载保存为 shop_data.json 后拉进项目即可
    NSString* shopDataNSUrl = [[NSBundle mainBundle] pathForResource:@"shop_data" ofType: @"json"];
    NSURL* shopDataUrl = [NSURL fileURLWithPath: shopDataNSUrl];

    if (shopDataUrl != nil) {
        [liqManager setShopData:shopDataUrl];
    }
}

@end
````

### 购物车集成 CartViewController

购物车一样使用 `LIQView` 就可以实现，使用 `reloadCart()` 即可。LionIQ 不需要另外登陆也不需要迁移你的用户信息，所有购物车只需要用户ID，用 `LIQManager` 的 `setAppUserId` 方式。

````
#import "CartViewController.h"

@interface CartViewController () <LIQViewDelegate>

@end

@implementation CartViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 针对 storyboard 7plus/6plus bug
    self.webviewPlaceholder.frame = self.view.frame;

    // 添加到本view
    self.liqview = [[LIQView alloc] initWithFrame:[self webviewPlaceholder].frame];
    [self.view addSubview: self.liqview];

    // 设置代理
    self.liqview.delegate = self;

    // 加载购物车
    [self.liqview reloadCart];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];

    // LionIQ 单列管理对象
    LIQManager* lm = [LIQManager defaultManager];

    // 让用户登录CheckForLogin 保存用户 userId 再调用 refreshShopUser 方式
    [lm setAppUserIdWithAppUserId: "THIS_USER_ID"];

    // 更新购物车用户
    [self.liqview refreshShopUser];
}
@end

````

### 搜索集成 SearchViewController
商城搜索组件，可以查看项目 Demo 项目在商城页面的搜索框绑定点击事件过度到搜索页面。点击 Cancel 按钮后可以通过代理方式 pop 回到商城。

````
#import "SearchViewController.h"

@interface SearchViewController () <LIQViewDelegate>

@end

@implementation SearchViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 针对 storyboard 7plus/6plus bug
    self.webviewPlaceholder.frame = self.view.frame;

    // 添加到本view
    self.liqview = [[LIQView alloc] initWithFrame:[self webviewPlaceholder].frame];
    [self.view addSubview: self.liqview];

    // 设置代理
    self.liqview.delegate = self;

    // 实现搜索
    [self.liqview reloadSearch];
}

// 代理方式: 由于`SearchViewController`不是最底下的`viewController`点击`SearchViewController`搜索的cancelButton 执行 `webviewDidAddToCart` 代理方式 pop 掉当前的`SearchViewController`,回到`shopViewController`

- (void)webviewSearchDidCancel {
    [self.navigationController popViewControllerAnimated:true];
}

@end
````


## 插件 API

#### LIQManager

插件管理对象，负责管理应用 `APP_KEY`, `APP_SECRET` 等等，以及功能：
    + 自动更新模版
    + 载入离线商城信息
    + 响应消费者登陆／退出

- `+(id) defaultManager`
    + 单列对象
    + returns: `LIQManager`

- 公开变值
    + params
    
        param | class | notes
        `appKey` | `String?` | 应用 APP_KEY, 从后台获取。只读变值.
        `appSecret` | `String?` | 应用 APP_SECRET, 从后台获取。只读变值.
        `appUserId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

- `-(void) setAppKey:(String *)appKey, appSecret:(String*)appSecret`
    + 设置应用 APP_KEY, APP_SECRET
    + params
        
        param | class | notes
        `appKey` | `String?` | 应用 APP_KEY, 从后台获取。只读变值.
        `appSecret` | `String?` | 应用 APP_SECRET, 从后台获取。只读变值.

- `-(void) setAppUserId:(String *)appUserId`
    + 设置消费者用户

        param | class | notes
        ------|-------|--------
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

- `-(void) setShopData:(NSURL *) shopDataURL`
    + 设置商城首页信息，JSON 档案读取。 从后台下载。
    + params; 

        param | class | notes
        ------|-------|-------
        `shopDataURL` | `NSURL` | JSON 档案的 `NSURL` 路径

- `-(void) getUpdates`
    + 查询、下载、更新模版。会向服务器查看有没有新版本更新，若有的话会自行下载到项目沙盒，并且更新插件模版。

#### LIQView

插件显示器，用来显示商城及购物车

- `- (void)initWithFrame:(CGRect *)frame`
    + 初始化 `LIQWebview` 实例
    + params

        param | class | notes
        ------|-------|---------
        `frame` | `CGRect` | 实例出现的 `frame`

    + returns: 无

- `- (void)reloadShop`
    + 载入商城页面
    + params: 无
    + returns: 无

- `- (void)reloadCart`
    + 载入购物车页面
    + params: 无        
    + returns: 无

- `- (void)reloadSearch`
    + 载入商城搜索页面
    + params: 无        
    + returns: 无
    
- `- (void)reloadShopUser`
    + 刷新商城页面，用来更新消费者用户登陆状态
    + params: 无        
    + returns: 无

#### LIQViewDelegate 代理方式

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

- `- (void)webviewDidCancel`
    + 点击取消按钮事件，只用在`Search`组件

