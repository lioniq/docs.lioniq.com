# Swift 3.0

## 项目引入

在 `ViewController` 中引入插件生成 `LIQWebview` 的实例就可以引入商城、及购物车界面。
(使用前请到官网后台申请生成 `APP` 的 `APP_KEY` 和 `APP_SECRET` 帐号权限)

````
import UIKit
import Lioniq

class ShopViewController: UIViewController {

    // Storyboard 添加一个普通 View 作为占位 (目前无法直接 Storyboard 使用)
    @IBOutlet weak var webviewPlaceholder: UIView!

    // 插件 webview 
    var liqview: LIQView!
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // 针对 storyboard 7plus/6plus bug
        self.webviewPlaceholder.frame = self.view.frame 

        // 添加到本view
        self.liqview = LIQView(frame: webviewPlaceholder.frame)
        self.view.addSubview(self.liqview)

        // LionIQ 单列管理对象
        let liqManager = LIQManager.defaultManager
        liqManager.setAppKey(appKey: "我的_APP_KEY", appSecret: "我的_APP_SECRET")

        // 设置代理
        self.liqview.delegate = self 

        // 实现商城
        self.liqview.reloadShop

        // OPTIONAL 可选: 
        // 自动更新模版
        liqManager.getUpdates()

        // OPTIONAL 可选: 
        // 载入商城离线信息, 从后台下载保存为 shop_data.json 后拉进项目即可
        if let shopDataUrl = Bundle.main.url(forResource: "shop_data", withExtension: "json") {
            liqManager.setShopData(shopDataURL: shopDataUrl)
        }
    }
}
````

## 插件 API

#### LIQManager

插件管理对象，负责管理应用 `APP_KEY`, `APP_SECRET` 等等，以及功能：
    + 自动更新模版
    + 载入离线商城信息
    + 响应消费者登陆／退出

- `defaultManager`
    + 单列对象
    + returns: `LIQManager`

- 公开变值
    + params
    
        param | class | notes
        `appKey` | `String?` | 应用 APP_KEY, 从后台获取。只读变值.
        `appSecret` | `String?` | 应用 APP_SECRET, 从后台获取。只读变值.
        `appUserId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

- `func setAppKey(appKey: String, appSecret: String)`
    + 设置应用 APP_KEY, APP_SECRET
    + params
        
        param | class | notes
        `appKey` | `String?` | 应用 APP_KEY, 从后台获取。只读变值.
        `appSecret` | `String?` | 应用 APP_SECRET, 从后台获取。只读变值.

- `func setAppUserId(appUserId: String)`
    + 设置消费者用户

        param | class | notes
        ------|-------|--------
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

- `func setShopData(shopDataURL: URL)`
    + 设置商城首页信息，JSON 档案读取。 从后台下载。
    + params; 

        param | class | notes
        ------|-------|-------
        `shopDataURL` | `URL` | JSON 档案的 `URL` 路径

- `func getUpdates()`
    + 查询、下载、更新模版。会向服务器查看有没有新版本更新，若有的话会自行下载到项目沙盒，并且更新插件模版。

#### LIQView

插件显示器，用来显示商城及购物车

- `func init(frame: CGRect)`
    + 初始化 `LIQWebview` 实例
    + params

        param | class | notes
        ------|-------|---------
        `frame` | `CGRect` | 实例出现的 `frame`

    + returns: 无

- `func reloadShop()`
    + 载入商城页面
    + params: 无
    + returns: 无

- `func reloadCart()`
    + 载入购物车页面
    + params: 无
    + returns: 无        

- `func reloadShopUser()`
    + 刷新商城页面，用来更新消费者用户登陆状态
    + params: 无
    + returns: 无


#### LIQViewDelegate 代理方式

所有插件事件通过 `LIQWebviewDelegate` 代理方式监听：

- `func webviewDidMain()`
    + 插件载入主页路由事件，列如: 从产品页面返回商城首页而显示 `NavigationBar` 及 `TabBar`. 商城、购物车两个主页都会响应这个事件.

- `func webviewDidItemDetail()`
    + 插件载入产品详情路由事件，列如: 从商城页面到详情页面时隐藏 `NavigationBar` 及 `TabBar`.  商城、购物车两个主页都会响应这个事件.

- `func webviewDidLoadItem(itemData: Dictionary<String, AnyObject>)`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `itemData` | `Dictionary<String, AnyObject>` | 产品信息 JSON, 请查看 [ItemData](json_objects.md)

- `func webviewDidAddToCart(cartItemData: Dictionary<String, AnyObject>)`
    + 加入购物车事件, 若用户未登陆时可以在这个事件显示登陆／注册界面。也可以用来响应 UI 效果给用户, 列如: TabBar 购物车图标上添加 badge 显示有产品加入了购物车. 
    + params
        
        param | class | notes
        ------|-------|---------
        `cartItemData` | `Dictionary<String, AnyObject>` | 购物车列表项信息 JSON, 请查看 [CartItemData](json_objects.md)

- `func webviewDidCheckout()`
    + 插件载入结算路由事件

- `func webviewDidOrder(orderData: Dictionary<String, AnyObject>)`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `orderData` | `Dictionary<String, AnyObject>` | 订单信息 JSON, 请查看 [OrderData](json_objects.md)










