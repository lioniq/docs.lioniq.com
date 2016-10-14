# 如何储存电商订单为JSON属性

一般电商应用数据库我们可以现象订单对象 `Order` 与产品对象 `Item` 的关联，但是也有些情况把订单信息直接以静态JSON属性储存入数据库会有优势: 

- 产品价格、信息更改
- 产品删除或下架

把关系数据列如订单列表项 `order_items` 直接以JSON属性存入数据库会失去所谓订单 `Order` 于产品 `Item` 的关联。因而订单信息的互动有限，反而宁愿订单信息生成后固定更改: 

- 用户查看下过的订单信息，产品，单价
- 运营团队更新订单状态 (未付、发货、完成)

另外，在移动APP会经常需要把数据转换成JSON格式。因此，直接把订单信息储存为JSON属性是个方便的选择。我们在 LionIQ 数据狮使用的技术是 Postgresql + Ruby/Rails 5.0, 以下范例以此为主，使用其他框架的同学们可以在底下参考其他框架的JSON属性使用法。

### Model 数据部署

创建数据如下: 

````
  rails g model Order order_no:uniq user:belongs_to order_infos:jsonb
````

创建一般的 `Order` 对象，再加上了一个 `order_infos` 以 `jsonb` 的属性装订单信息. [1]

````
# app/models/order.rb
class Order < ApplicationRecord
  belongs_to :shop_user
  
  # jsonb accessors
  store :order_infos, accessors: [:address, :order_items, :total_amount]
  store_accessor :order_infos 
end
````

数据定义里我们用了 `store_accessor` 及 `store` 可以透过 `order_infos` 参数获取 `JSON` 数据，以及相应 `order_infos` 内的参数。 

````
lioniq$ rails c
Running via Spring preloader in process 29470
Loading development environment (Rails 5.0.0)
irb(main):001:0> o = Order.new
=> #<Order id: nil, key: nil, archived: false, user_id: nil, order_infos: {}, created_at: nil, updated_at: nil, order_no: nil>

irb(main):002:0> o.order_infos
=> {}

irb(main):004:0> o.address = "98 Yanping Lu"
=> "98 Yanping Lu"

irb(main):005:0> o.order_items = [{key: "some_item_key", title: "cool tshirt", price: 3000, qty: 1}, {key: "another_key", title: "air jordan V", price: 10000, qty: 1}]
=> [{"key"=>"some_item_key", "title"=>"cool tshirt", "price"=>3000, "qty"=>1}, {"key"=>"another_key", "title"=>"air jordan V", "price"=>10000, "qty"=>1}]
irb(main):006:0> 

````

### Controller 控制器

[LionIQ](https://lioniq.com/) 插件会以 JSON 格式传订单 `Order` 数据，之后可以传送到你的后台 API 然后保存为以上定义的 `Order` 数据. 因为我们在 `model` 里定义了 `store_accessor` 可以很方便的定义 `strong parameters` 可允许的 JSON 参数传给 `Order` 初始化方式。

````
# app/controllers/orders_controller.rb
class OrdersController < ApplicationController

    # POST /orders
    # POST /orders.json
    def create
        @order = Order.new(order_params)

        # associate user, depending on your authentication
        @order.user = current_user 

        if @order.save 
            render :show, status: :created, location: @order
        else
            render json: @order.errors, status: :unprocessable_entity
        end
    end 

    private

    def order_params
        item_params = [:key, :title, :price]
        params.require(:order).permit(:order_no, :total_amount, :address, 
            order_items: [{item: item_params}, :qty])
    end
end
````


#### 参考
- [1] Rails / Postgresql JSONB
  - http://nandovieira.com/using-postgresql-and-jsonb-with-ruby-on-rails
  - https://segmentfault.com/a/1190000002911580




