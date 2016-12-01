# LionIQ 数据狮 Android SDK

LionIQ 数据狮专注于移动端电商插件开发，让任何APP快速完善电商功能。

## 简介
本Android库包含: 

- `libs/lioniq.jar` Android 插件
- `example/LioniqAndroidDemo` 简单接入示例，该示例仅供参考。

## 版本要求
Android SDK 要求 android 4.0.3 及以上版本.

## 安装

建议使用 build.gradle 安装，也支持手动导入 lioniq.jar.

### 使用 build.gradle 安装
    //TODO:待完善


### 手动导入
1. 下载 SDK 后, 直接拖动 `lioniq.jar` 到项目libs目录里。

2. 右键点击libs目录里的`lioniq.jar`，在弹出菜单中点击Add As Library.

3. 在项目的build.gradle里面添加项目依赖:

	````
    	compile files('libs/lioniq.jar')
	````