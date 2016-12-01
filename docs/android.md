# Android App

## 项目引入
在`Activity`中引入插件生成`LIQView`的实例就可以引入商城、及购物车界面。
(使用前请到官网后台申请生成 `APP` 的 `APP_KEY` 和 `APP_SECRET` 帐号权限)

### 商城集成

````
import com.lioniq.LIQJavascriptInterface;
import com.lioniq.LIQManager;
import com.lioniq.LIQView;

public class ShopActivity extends AppCompatActivity {

	// LIQView 实例
	public LIQView liqView;

	// LionIQ 协议方式 接口
    public LIQJavascriptInterface liqJavascriptInterface;

    // LionIQ 单列管理对象
    public LIQManager liqManager = LIQManager.getInstance();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_shop);

        // 设置 appKey, appSecret  this 为当前上下文 Context
        liqManager.setAppKey(this, "我的_APP_KEY", "我的_APP_SECRET");

        // 初始化 LIQView, 协议方式 接口实现
        liqView = new LIQView(this, new LIQJavascriptInterface() {

            @JavascriptInterface
            @Override
            public void didAddToCart(String itemData) {
                
            }

            @JavascriptInterface
            @Override
            public void didItemDetail(String item) {

            }

            @JavascriptInterface
            @Override
            public void didLoadItemDetail(String itemData) {

            }

            @JavascriptInterface
            @Override
            public void didMain(String didmain) {
            }
        });

        // 添加到本view
        WebView view = (WebView) findViewById(R.id.webview);
        view.addView(liqView);

        // 加载shopView 实现商城
        liqView.reloadShop();

        // OPTIONAL 可选: 
    	// 载入商城离线信息, 从后台下载保存为 shop_data.json 后拉进项目放在资源文件夹assets即可
    	// this 为当前上下文 Context
        liqManager.setShopData(this, getShopDataStr());

       
        // 更新 userId
        liqManager.set_appUserId("我的_USER_ID");
        liqView.refreshShopUser();
    }

    // shop_data.json to string 
    private String getShopDataStr() {
        try {
            InputStream shopData = this.getAssets().open("shop_data.json");
            String shopDataStr = liqManager.convertStreamToString(shopData);
            return shopDataStr;

        } catch (IOException e) {
            e.printStackTrace();
            return "";
        }
    }


    // 点击系统Back键的处理 浏览的网页回退而不是退出浏览器 webview can go back
    public boolean onKeyDown(int keyCode, KeyEvent event) {
       if ((keyCode == KeyEvent.KEYCODE_BACK) && liqView.canGoBack()) {
           liqView.goBack();
            return true;
            }
        return super.onKeyDown(keyCode, event);
    }   
}
````

````
### 购物车集成 CartActivity

import com.lioniq.LIQJavascriptInterface;
import com.lioniq.LIQManager;
import com.lioniq.LIQView;

public class CartActivity extends AppCompatActivity {

	// LIQView 实例
    public LIQView liqView;
	
	// LionIQ 协议方式 接口
    public LIQJavascriptInterface liqJavascriptInterface;

    // LionIQ 单列管理对象
    public LIQManager liqManager = LIQManager.getInstance();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_cart);


        // NOTE: 只需要绑定一次，商城绑定过不需要在购物车再绑定.
        liqManager.setAppKey(this, "我的_APP_KEY", "我的_APP_SECRET");

        // 初始化 LIQView, 协议方式 接口实现
        liqView = new LIQView(this, new LIQJavascriptInterface() {

            @JavascriptInterface
            @Override
            public void didAddToCart(String itemData) {
                
            }

            @JavascriptInterface
            @Override
            public void didItemDetail(String item) {

            }

            @JavascriptInterface
            @Override
            public void didLoadItemDetail(String itemData) {

            }

            @JavascriptInterface
            @Override
            public void didMain(String didmain) {
                
            }

            @JavascriptInterface
            @Override
            public void webviewDidCheckout() {

            }

            @JavascriptInterface
            @Override
            public void webviewDidOrder(String orderData) {

            }

        });

        // 添加到本view
        WebView view = (WebView) findViewById(R.id.webview);
        view.addView(liqView);

        // 实现购物车
        liqView.reloadCart();
    }

    @Override
    protected void onStart() {
        super.onStart();

        // 购物车出现时通知用户登陆状态
        LIQManager.getInstance().set_appUserId("USER_ID");
        liqView.refreshShopUser();
    }

    // 点击系统Back键的处理 浏览的网页回退而不是退出浏览器 webview can go back
    public boolean onKeyDown(int keyCode, KeyEvent event) {
       if ((keyCode == KeyEvent.KEYCODE_BACK) && liqView.canGoBack()) {
           liqView.goBack();
            return true;
            }
        return super.onKeyDown(keyCode, event);
    }
}
````

````
### 搜索集成 SearchActivity
商城搜索组件，可以查看项目 Demo 项目在商城页面的搜索框绑定点击事件过度到搜索页面。点击 Cancel 按钮后可以通过代理方式 pop 回到商城。

import com.lioniq.LIQJavascriptInterface;
import com.lioniq.LIQManager;
import com.lioniq.LIQView;

public class SearchActivity extends AppCompatActivity {

	// LIQView 实例
    public LIQView liqView;
	
	// LionIQ 协议方式 接口
    public LIQJavascriptInterface liqJavascriptInterface;

    // LionIQ 单列管理对象
    public LIQManager liqManager = LIQManager.getInstance();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_search);

        // 初始化 LIQView, 协议方式 接口实现
        liqView = new LIQView(this, new LIQJavascriptInterface() {
            @JavascriptInterface
            @Override
            public void webviewSearchDidCancel() {
            	// TODO: 返回首页 ShopActivity
            }


        });

        // 添加到本view
        WebView view = (WebView) findViewById(R.id.webview);
        view.addView(liqView);

        // 实现搜索
        liqView.reloadSearch();
    }

    // 点击系统Back键的处理 浏览的网页回退而不是退出浏览器 webview can go back
    public boolean onKeyDown(int keyCode, KeyEvent event) {
       if ((keyCode == KeyEvent.KEYCODE_BACK) && liqView.canGoBack()) {
           liqView.goBack();
            return true;
            }
        return super.onKeyDown(keyCode, event);
    }
}
````

## 插件 API

#### LIQManager

插件管理对象，负责管理应用 `APP_KEY`, `APP_SECRET` 等等，以及功能：
    + 自动更新模版
    + 载入离线商城信息
    + 响应消费者登陆／退出

- `getInstance`
    + 单列对象
    + returns: `LIQManager`

- 公开变值
    + params
    
        param | class | notes
        `appKey` | `String?` | 应用 APP_KEY, 从后台获取。只读变值.
        `appSecret` | `String?` | 应用 APP_SECRET, 从后台获取。只读变值.
        `appUserId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

- `public void setAppKey(String appKey, String appSecret)`
    + 设置应用 APP_KEY, APP_SECRET
    + params
        
        param | class | notes
        `appKey` | `String?` | 应用 APP_KEY, 从后台获取。只读变值.
        `appSecret` | `String?` | 应用 APP_SECRET, 从后台获取。只读变值.

- `public void setAppUserId(String appUserId)`
    + 设置消费者用户

        param | class | notes
        ------|-------|--------
        `userId` | `String?` | 消费者用户 id, 若 `nil` 的话会当做用户未登陆

- `public void setShopData(String shopDataURL)`
    + 设置商城首页信息，JSON 档案读取。 从后台下载。
    + params; 

        param | class | notes
        ------|-------|-------
        `shopDataURL` | `String` | JSON 档案的 `URL` 路径

- `public void getUpdates()`
    + 查询、下载、更新模版。会向服务器查看有没有新版本更新，若有的话会自行下载到项目沙盒，并且更新插件模版。

#### LIQView

插件显示器，用来显示商城及购物车

- `public void reloadShop()`
    + 载入商城页面
    + params: 无
    + returns: 无

- `public void reloadCart()`
    + 载入购物车页面
    + params: 无
    + returns: 无        

- `public void reloadSearch()`
    + 载入商城搜索页面
    + params: 无
    + returns: 无

- `public void reloadShopUser()`
    + 刷新商城页面，用来更新消费者用户登陆状态
    + params: 无
    + returns: 无


#### LIQViewDelegate 协议方式 接口

所有插件事件通过 `LiqJavascriptInterface` 协议方式 监听JS：

- `public void webviewDidMain()`
    + 插件载入主页路由事件，列如: 从产品页面返回商城首页而显示 `NavigationBar` 及 `TabBar`. 商城、购物车两个主页都会响应这个事件.

- `public void webviewDidItemDetail()`
    + 插件载入产品详情路由事件，列如: 从商城页面到详情页面时隐藏 `NavigationBar` 及 `TabBar`.  商城、购物车两个主页都会响应这个事件.

- `public void webviewDidLoadItem(String itemData)`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `itemData` | `String` | 产品信息 JSON, 请查看 [ItemData](json_objects.md)

- `public void webviewDidAddToCart(String cartItemData)`
    + 加入购物车事件, 若用户未登陆时可以在这个事件显示登陆／注册界面。也可以用来响应 UI 效果给用户, 列如: TabBar 购物车图标上添加 badge 显示有产品加入了购物车. 
    + params
        
        param | class | notes
        ------|-------|---------
        `cartItemData` | `String` | 购物车列表项信息 JSON, 请查看 [CartItemData](json_objects.md)

- `public void webviewDidCheckout()`
    + 插件载入结算路由事件

- `public void webviewDidOrder(String orderData)`
    + 插件载入产品信息.  商城、购物车两个主页都会响应这个事件.
    + params
        
        param | class | notes
        ------|-------|---------
        `orderData` | `String` | 订单信息 JSON, 请查看 [OrderData](json_objects.md)

- `public void webviewDidCancel()`
    + 点击取消按钮事件，只用在`Search`组件



