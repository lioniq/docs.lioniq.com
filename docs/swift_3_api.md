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
    var webview: LIQWebview!
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // 添加到本view
        self.webview = LIQWebview(frame: webviewPlaceholder.frame)
        self.view.addSubview(self.webview)

        // 设置代理
        self.webview.delegate = self 
        
        // 实现商城
        self.webview.reloadShop(key: "我的_APP_KEY", secret: "我的_APP_SECRET", userId: "消费者_USER_ID")
        
    }
}
````

## 插件 API

- `func init(frame: CGRect)`
    + 初始化 `LIQWebview` 实例
    + params

        param | class | notes
        ------|-------|---------
        `frame` | `CGRect` | 实例出现的 `frame`

    + returns: 无

- `func reloadShop(_ key: String, secret: String, userId: String?)`
    + 载入商城页面
    + params
        
        param | class | notes
        ------|-------|--------
        `key` | `String` | 应用 APP_KEY, 从后台获取
        `secret` | `String` | 应用 APP_SECRET, 从后台获取
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

    + returns: 无

- `func reloadCart(_ key: String, secret: String, userId: String?)`
    + 载入购物车页面
    + params
        
        param | class | notes
        ------|-------|--------
        `key` | `String` | 应用 APP_KEY, 从后台获取
        `secret` | `String` | 应用 APP_SECRET, 从后台获取
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆
        
    + returns: 无

- `func reloadShopForUser(_ userId: String?)`
    + 刷新商城页面，用来更新消费者用户登陆状态
    + params
        
        param | class | notes
        ------|-------|-------
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆
        
    + returns: 无
    
- `func reloadCartForUser(_ userId: String?)`
    + 刷新购物车页面，用来更新消费者用户登陆状态
    + params
        
        param | class | notes
        ------|--------|--------
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆
        
    + returns: 无


#### 代理方式

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
        `itemData` | `Dictionary<String, AnyObject>` | 产品信息 JSON, 请查看 `itemData`

- `func webviewDidAddToCart(cartItemData: Dictionary<String, AnyObject>)`
    + 加入购物车事件, 若用户未登陆时可以在这个事件显示登陆／注册界面。也可以用来响应 UI 效果给用户, 列如: TabBar 购物车图标上添加 badge 显示有产品加入了购物车. 
    + params
        
        param | class | notes
        ------|-------|---------
        `cartItemData` | `Dictionary<String, AnyObject>` | 购物车列表项信息 JSON, 请查看 `cartItemData`

- `func webviewDidCheckout()`
    + 插件载入结算路由事件

- `func webviewDidOrder(orderData: Dictionary<String, AnyObject>)`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `orderData` | `Dictionary<String, AnyObject>` | 订单信息 JSON, 请查看 `orderData`










