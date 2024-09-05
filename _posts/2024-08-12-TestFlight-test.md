---
layout: post
title: TestFlight上架测试记录
categories:
  - testflight
  - IOS
description: 苹果app开发做testflight上架测试
keywords: testflight
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
### 一、上架网站及身份认证

要进行TestFlight上架，需要将打包好的软件（ipa格式，在软件打包部分会详细介绍）上传到特定网站，而且要求具有开发者账号的用户才能使用TestFlight上架分发的功能，在上架操作之前，先要完成网站资质的认证。

  

1. 上架网站为： App Store Connect

2. 开发者账号验证

使用开发者账号关联的apple id登录，并提交身份证照片，以及填写出生地等信息，完成之后点击提交，等待后台验证审核。审核时间为一天左右。

3. 网站入口：

- https://appstoreconnect.apple.com/login 直接从App Store Connect 网站进入
    
- 从https://developer.apple.com网站进入，通过accoun页面左侧的‘计划资源’字段下方，有App Store Connect 的链接入口
图1

### 二、上传软件选择

- **Xcode**：最常用的上传工具是Xcode，这是苹果官方提供的集成开发环境（IDE）。通过Xcode，你可以直接将应用构建并上传到App Store Connect。
    
- **Transporter**: 用XCode打包并上传，总会很容易出错各种莫名奇妙的错误，而且有时无解。经过多次尝试，发现最好的解决方案是不要使用XCode来上传，而使用[Transporter](https://apps.apple.com/us/app/transporter/id1450874784?mt=12)能很大程度降低出错，而且在使用xcode打包完ipa进行Distribute App时，即可以选择App Store Connect ， 也可以选择Ad Hoc， 多了一种规避错误的选择。
    

### 三、App ID 创建 与 Bundle ID 填写

在处理**App ID**和**Bundle ID**的创建时，通常建议先创建**App ID**，然后在Xcode中填写**Bundle ID**，以确保两者一致。具体步骤为

1. 首先在Apple Developer网站上创建App ID：
    
    1. 在开发者网站https://developer.apple.com 的account下的 ‘证书、标识符和描述文件’下点击‘证书（英文）‘， 打开 **Certificates, Identifiers & Profile** 页面。
        
    2. 在左侧的 `Identifiers` 下，选择 `App IDs`，点击页面右上角的 `+` 按钮来创建新的App ID。
        
    3. 填写App ID信息：App ID Prefix: 系统自动生成，通常是你的团队ID，而App ID Suffix: 选择 `Explicit App ID`，然后输入你计划在Xcode中使用的Bundle ID，例如`com.yourcompany.yourappname`。
        
2. 然后在Xcode中设置Bundle ID：
    
    1. 打开你的Xcode项目，选择项目导航器中的应用程序目标（Target），在 `General` 选项卡下，找到 `Identity` 部分，在 `Bundle Identifier` 字段中输入与你创建的App ID一致的Bundle ID（例如`com.yourcompany.yourappname`），确保这个Bundle ID与Apple Developer账户中的App ID完全一致。
        
    2. 也可以在 Signing & Capabilities选项卡下填写。
        

说明：Apple的系统会检查App ID和Bundle ID的一致性。如果先在Apple Developer网站上创建App ID，你可以确保它完全符合你预期的格式和需求。先创建App ID可以避免与其他项目冲突，并确保Bundle ID是唯一的。有了明确的App ID后，配置证书、描述文件等步骤都会更加顺畅，不容易出错。

  

### 四、证书制作与安装

#### 证书签名请求CSR 文件制作：

在 macOS 电脑使用 **钥匙串访问（Keychain Access）** 应用程序，创建一个 CSR（证书签名请求）文件，流程如下：

1. 在mac 电脑上打开 **应用程序** 文件夹，然后找到 **实用工具** 文件夹。在 **实用工具** 文件夹中，打开 **钥匙串访问** 应用程序。
    
2. 在 **钥匙串访问** 应用程序中，单击菜单栏 **钥匙串访问** > **证书管理** > **从证书颁发机构请求证书**。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmY2MTdmYTg3ZjNkNDNlMzVlMzk4NDdjMWY2YTk5OWNfREFyZ0doYnY5TUtMaEFUcW9lYkJwd2RhNHRZZm1UQWFfVG9rZW46TXVLRmJ6NEYzb3dJaXF4eUVGSmNRT05vbjVnXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

3. **用户电子邮件地址** 填写您的常用邮箱，**常用名称** 填写您的名称，选择 **存储到磁盘**，然后单击 **继续**。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=NDQwMjRlMDg1ZDE1NDY2ODFmZjQyYzM1MGQyODk5YzFfbEgzbnhoMWtDNE85OVlJbVRIR3F0S2FZZGxUMFpSMnZfVG9rZW46SnFzcGJWSENWb3NKWXB4REIzRWM3Q2RybkJiXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

4. 保存 **CertificateSigningRequest.certSigningRequest** 文件至本地，完成 CSR 文件创建。
    

#### 应用发布证书制作：

1. 在开发者网站https://developer.apple.com 的account下的 ‘证书、标识符和描述文件’下点击‘证书（英文）‘， 打开 **Certificates, Identifiers & Profile** 页面，选择 **Certificates**，单击 **+**。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=NTZlMmI4YjUzZTY0ODNmYTUyZTAwNjI4YTc2YmFjM2FfTVFqZGxUM2dEd3VLUVhyMkk4YW9XZFNUNnZCUUhQSFNfVG9rZW46TTRsaWJjTlFkbzY2TjV4dENRdGNsVlBFbjJlXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

2. 选择 **iOS Distribution（App Store and** **Ad Hoc****）**，单击 **Continue**。
    
3. 如果无法选择，提示证书数量超过限制，请返回上一页，删除多余的证书。证书只在应用构建和上传阶段会使用，因此删除证书不会影响已上架应用的正常使用。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=YzE4Mzk1Mjk1OTM5MjAwZThhNjQyNzI1MjEzNTFhZGRfRFUwVldYTXJaemYzTHJRSHRaUWtKN1hQalpGaXNyeFJfVG9rZW46WXJFOGJaR2R0b2NhYWx4cUR3V2NPdzJUbkNlXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

4. 单击 **Choose File**，选择刚才生成的 CSR 文件，单击 **Continue**。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=NWU5MWNlMTg5NGNjNjExZjY0NmE4ZGJiMTJhOGIzZjZfM2ltbWJOdVJ6UW1PQ2FHQXpBWmR0ZUIzamtudEFVTG1fVG9rZW46UjZjamJJTmNWb1h4c3F4UzVtZGNKVUgzbkdiXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

5. 单击 **Download**，下载 **ios_distribution.cer** 文件到本地
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=MDE0OWFkMjIyZTY1OTM2ZTk2NGZjN2RlMTljYzE5ZTZfdVBETmdYczF2Q2ZMeFV2RGZpR0YwcExJWnNEcUtZVzJfVG9rZW46SllTM2JVV05Kb2xuNTF4T2tzNGNHeHA0bkpiXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

6. 双击下载的 **ios_distribution.cer（名称您可以自定义）** 文件，它将会自动导入到 **钥匙串访问** 应用中，如下图所示：

到此处已经可以满足tesflight上架的要求，如果需要导出为 .p12 文件， 可以参考：

https://developer.tuya.com/cn/docs/iot/ios-push?id=K989rskj262hu

#### 描述文件（.mobileprovision 格式）制作

1. 参考上一步，打开 **Certificates, Identifiers & Profile** 页面，选择 **Profiles**，单击 **+**。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=OWZiMWQyMmU0MWZjNDI5Nzg0ZGFkMzc5MWIwZjcxOGJfYUVvSGRxckxISVdRMUo2NkJ2Z3pwT2VQbkFGR0FPNFlfVG9rZW46QWhkNGJjUGl4b3hKMWF4a01iSmNXRUVFbjZkXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

2. 选择 **App Store Connect**，单击 **Continue**。选择您开始创建的 App ID 单击 **Continue ，**选择之前创建的应用发布证书(名字和图2对应)，单击 **Continue**。
    
3. 在 **Provisioning Profile Name** 处可填写您的 App 名称，单击 **Generate**。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=OTk4ODZhYzRlOWY5NGY2MjM0YTg3MTQyM2RmNmQyY2ZfaDgxdWxaYnA3QVh4ZExOc1JkVXdlWjFwVVRUQ0xBeTJfVG9rZW46UTM0QmJaakczb3I1c3V4VVhrb2NPdnJGbnFnXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

4. 单击 **Download**，下载配置文件，完成文件创建。
    
5. 当成功下载了 `xztech_AdHoc.mobileprovision` 文件（描述文件）到本地后，需要将其配置到你的Xcode项目中，直接双击下载的 `xztech_AdHoc.mobileprovision` 文件，它会自动导入到Xcode中。Xcode会处理并识别这个描述文件，并将其与对应的证书和App ID关联，以便能够使用这个描述文件进行应用签名并进行TestFlight或Ad Hoc分发。
    

### 五 IPA文件制作

1. 在Xcode的菜单栏选择`Product`，然后选Archive进行打包：
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=YTUzYmQ1YmI0YTdjODE4MTg3NDgzY2MyZjg4ZWIyMTlfVmE4UFY2TFFZM3lEUFpGYThFN0NUYnNUb2hqZzROd0FfVG9rZW46WGgzZmJnYnNBb2lIdk54cVZkV2NhSG1MbnVnXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

2. 打包完成，会弹出以下对话框，就可以把应用上传到App Store Connect了。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=NjcyNGU4OWNkOWY5Mzk2Mjk1OGFmNmZkNjA2MDMwOThfdERNNkVobkVJa1lRdkl6YzZySnJVZWRuTU1UeU1wdGZfVG9rZW46QTU1bmJqMElYb1p0NVV4aHE2bGNjZjdLblRnXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

上面这个对话框可以按下面的方式打开：

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=YjJhNjIzNjcxNTVjZmY1NzhiNmY1Yjk4ODJjYmJkM2RfNWZtR2lCMU5IcmNWblNiek5hd3F5d3VDZmpPdk9KZVhfVG9rZW46TDNxbGJYUHJUb3FHeFV4ZUZLRWN6cG8ybk5oXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

3. 打包完后，点击`Distribute App` 上传。
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=YmU1OTI2MjIxMzk1MTY2OTM0N2FkODAxNjFkMTdiZmVfS21VRXFOaG1adE9UalVPMlA2QzliOXNLSlJVYkVMVlZfVG9rZW46R1pSWmJweGYxb2ptMmV4TG9OOWMxMjFNbmxkXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=OTQ4MWExYzIwM2VlYWZhYzhlZDQwMTY2NDgzMGRhMmZfeWJaT1M1dGVlc0Z6SElSZGtibEwxanFUVGgydlBYSERfVG9rZW46VjF3VmJOdG5Wb1hRdzR4QkVZUWNkU0Yzbm1jXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

很多参考资料表明使用Xcode上传即可，但是特别容易出错，如果上传不成功的话，可以选择 Ad Hoc，接下来选Export

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=MDMzMTJjYzM4NzMwNDU3N2NlODgyMTY3ZTk4NjUyN2RfSEIxa1V3S1MxWDVKcXQ4RzV6d2NTc05BcUxPYWp5Z1FfVG9rZW46SnZaWGJaWG1Kb05CdGd4M2RibGMwa1ZwbnliXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

这样可以获得ipa文件。

ipa制作也可参考：

https://cloud.tencent.com/developer/article/1742366

### 六 上传到App Store Connect 以及 邀请测试

1. 上传之前先要在App Store Connect 的App 页面中 ，新建App
    

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=NGYzYmVlMjBiYTUxYjA3ZTIyNTBmM2U5ZTI3M2RhNDFfcHpXRHp5TVR3RllBNHUxYjQyT2dha29RRFVialJqQ3BfVG9rZW46TnVSaWJHSFFTbzBUbk54SmFCTWNSWDRCbkxmXzE3MjU1MjI5MzY6MTcyNTUyNjUzNl9WNA)

选择ios类型，主要语言随意选择，套装ID选择对应的AppID，SKU随意输入，用户访问权限选择完全访问权限，点击创建

2. 打开上传工具Transporter，登陆开发者账号，选择打包的ipa文件直接上传，上传完成后，App store Connect 的TestFlight 选项卡 中 将会显示构建版本信息
    
    
### 七 手机端使用

1. 使用者提供邮箱给软件开发（上架）人员。
    
2. 苹果手机(12pro及以上)安装TestFlight软件
    
3. 打开邮箱，打开巡智科技的邀请邮件，应该能看懂一下界面
    

4. 点击" View in TestFlight" ， 进入下一个界面，复制邀请码， 点击进入下个页面

5. 点击右上角的兑换，在弹出的输入框中填入邀请码，然后点击兑换，即可进入安装页面。

6. 点击页面中的安装按钮即可安装软件。
    
