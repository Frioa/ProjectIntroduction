# APM 

APM 是公司内部的应用性能检测平台。

## 整体流程

App 产生自动化测试报告后，会经过测试同学 Python Script 代理，将测试报告发送给 Dart 服务端。

![image](https://user-images.githubusercontent.com/33407522/168477929-4ccbfdfe-e66b-4585-aa4a-21b7f786aedc.png)


# 功能介绍

### APM 功能
1. UI 组件在线预览
2. 不同机型 UI 适配
3. 帧率检测功能
4. 其他测试工具类

### Dart Service 功能
1. pub 私服
2. Jenkins 打包
3. 机器人通知


### 1. 帧率检测功能
APM 的帧率检测功能依赖于自动化测试，自动化测试会收集帧率信息并保存在 Dart Service, 并在前端展示。

### 帧率检测流程
1. 首页会分析近10次，测试报告帧率变化。
![image](https://user-images.githubusercontent.com/33407522/168478313-33c67749-c95a-4a3e-9094-5afb54310fe5.png)
2. 测试报告列表页
分析每一条测试报告对应的业务数据
![image](https://user-images.githubusercontent.com/33407522/168478371-82cbd7a1-426a-474c-b542-741de32b7490.png)
3. 测试用例详情页
在测试用例详情页会记录对应的日志信息，根据日志信息方便我们定位代码位置。
![image](https://user-images.githubusercontent.com/33407522/168478420-c2a0354d-9750-4353-91be-0db1a1506854.png)
4. 飞书机器人通知

![image](https://user-images.githubusercontent.com/33407522/168478214-6d437b45-ba1f-412f-b651-7316caeba2ce.png)


### 2. pub 私服
私服方便团队管理自定义 Plugin
![image](https://user-images.githubusercontent.com/33407522/168478074-76b46a7d-ee43-4d99-8e4d-7e8f98d4c29c.png)

### 3. Jenkins
Jenkins 提供 Android、IOS 打包服务
![image](https://user-images.githubusercontent.com/33407522/168478152-e5b5bbe7-1963-4aef-82e1-dbd1be6ebac4.png)






