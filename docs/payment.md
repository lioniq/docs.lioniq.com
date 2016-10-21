# 接入支付

LionIQ 插件收到消费者结算下订单后会回传订单对象回给APP, 这时可以透过代理方式解析 `orderData` 的 `JSON` 字典。以下是我们在生产运行中的APP使用的范例: 

````
    /** 
        CartViewController.swift

        购物车结算界面跳转至 PaymentViewController 会传值`orderData: Dictionary<String, AnyObject>`到 PaymentViewController
    **/
    func webviewDidOrder(_ orderData: Dictionary<String, AnyObject>) {
        self.performSegue(withIdentifier: "payment", sender: orderData)
    }

    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "payment" {
            let paymentVC = segue.destination as! PaymentViewController
            paymentVC.orderData = sender as! Dictionary<String, AnyObject>?
        }
    }

````

然后在支付界面 `PaymentViewController` 解析订单信息，可以提供按钮给用户支付（如: 支付宝、微信、百度钱包、等等). 更多详情请查看[OrderData](json_objects.md)

````

    /**
        PaymentViewController.swift

        支付页面通常需要生成请求参数提交给支付平台. Pingxx 支付SDK需要以下参数: 
            - channel: String, 支付渠道
            - orderNo: String, 订单号码
            - amount: Int, 订单金额
    **/

    // 订单号
    let orderNo = orderData["order_no"] as? String

    // 实付款金额
    let payableAmount = orderData["total_amount"] as? Int

    // 渠道
    let channel = "alipay" 

    // 订单产品列表项 - 用于显示订单产品
    let orderItems = orderData["cart_items"] as? Dictionary<String, AnyObject>

````
