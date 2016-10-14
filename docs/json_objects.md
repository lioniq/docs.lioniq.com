
#### JSON 对象

- `itemData`

    key | value | notes
    ----|-------|--------
    `key` | `String` | id
    `title` | `String` | 产品名称
    `desc` | `String` | 产品描述
    `price` | `int` | 产品单价, 1 = ¥1.00
    `thumb_image_url` | `String` | 产品封面图小图 url
    `large_cover_photo_url` | `String` | 产品封面图 url 
    `item_footer` | `String` | 买家须知
    `photos` | `[photoData]` | 产品图片数组
    `detailPhotos` | `[photoData]` | 图文详情数组
    `sizes` | `[itemSizeData]` | 尺寸数组
    `colors` | `[itemColorData]` | 颜色数组

- `photoData`

    key | value | notes
    ----|-------|--------
    `key` | `String` | id
    `image_url` | `String` | 图片url, 保证正方形格式 320x320px
    `thumb_image_url` | `String` | 小图url, 120px 宽度
    `large_image_url` | `String` | 大图url, 750px 宽度

- `itemSizeData`

    key | value | notes
    ----|-------|-------
    `label` | `String` | 尺寸名称, e.g. "S", "M", "L"

- `itemColorData`
    
    key | value | notes 
    ----|-------|-------
    `label` | `String` | 颜色名称, e.g. "蓝色", "红色"

- `cartItemData`

    key | value | notes
    ----|-------|-------
    `qty` | `int` | 数量
    `color` | `String` | 款式颜色
    `size` | `String` | 款式尺寸
    `item` | `itemData` | 产品信息 JSON

- `orderData`
    
    key | value | notes
    ----|-------|-------
    `key` | `String` | id
    `order_no` | `String` | 订单号码
    `cart_items` | `[cartItemData]` | 购物列表项数组
    `address` | `addressData` | 地址对象
    `subtotal_amount` | `int` | 订单小计 (产品单价 x 数量，未计运费). 1 = ¥1.00
    `shipping_amount` | `int` | 运费总额. 1 = ¥1.00
    `total_amount` | `int` | 订单总金额. 1 = ¥1.00
    `fapiao` | `String` | 发票抬头
    `created_at` | `DateTime` | 订单下定日期
    `updated_at` | `DateTime` | 订单更新日期



