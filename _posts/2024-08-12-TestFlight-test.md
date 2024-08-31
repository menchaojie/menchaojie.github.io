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

## 什么是TestFlight

TestFlight 是一款在 2014 年被苹果收购的 iOS 测试工具。开发者可以通过 TestFlight 来邀请用户协助测试 App，待 App 完善之后，再提交正式版到 App Store，这对于开发者和用户来说都是有益处的。

## 步骤

1.  安装Transporter （mac, App store)，上传.ipa文件用的。
2.  证书...
3.   bundler id

下面是参考流程
1、申请P12证书

用苹果开发者账号登录appuploader软件后，选择证书进入，生成发布证书，设置证书密码，证书生成好了之后，下载P12文件。发布证书用于上架，证书有P12和mobileprovision两个文件，这两个文件都是必要的。

2、申请发布描述文件

在appuploader首页选择生成描述文件，创建成功后就可下载描述文件。（注：APP上架都对应一个不同的ID）

3、打包IPA

打包方式比较多，HBuilder打包、APICloud打包都可以，打包方法还是比较多。

4、上传IPA到iTunes Connect

使用开发者账号登录iTunes Connect，点击我的APP，创建APP，上传好IPA文件。

5、申请Test Flight测试

TF测试分为内部测试和外部测试，内部测试需要选择测试的苹果账号，内测用户需要使用邀请码；外部测试不需邀请码，审核需要一天左右，最快大概就三十分钟，审核通过后，生成测试网站链接，测试用户点击链接就可无需信任下载。


未完，待续...
这个一定要完成，加油， 时间是一点一点挤出来的
怎么挤一挤

怎么和飞书互动

测试公开仓库
[参考1: 阿里云..](https://developer.aliyun.com/article/1220212)
