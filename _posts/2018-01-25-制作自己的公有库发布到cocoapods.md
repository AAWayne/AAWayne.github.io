---
layout:     post
title:      制作自己的公有库发布到cocoapods
subtitle:   
date:       2018-01-25
author:     阿唯不知道
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - iOS
    - cocoapods
    - git
---
#### 这里以YWChooseAddressView为例

1、提交code到git仓库中，并打上tag版本号
    注意，如果是用git工具提交的demo，可直接在github上去发布realese版本
```
git tag -a 1.0.0
git push origin master
```
先看看我的目录结构（包含`测试demo`、`库`、`.podspec`、`LICENSE`等文件）：
![屏幕快照 2018-01-25 下午4.47.32.png](http://upload-images.jianshu.io/upload_images/2822163-9fb8a3a0a09ef1ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)
2、创建`podspec`执行
```
pod spec create YWChooseAddressView
```
2、配置`YWChooseAddressView.podspec`文件，直接打开或者利用vim编辑都行，注意`""`标点符号即可
```
Pod::Spec.new do |s|

  s.name         = "YWChooseAddressView"
  s.version      = "1.0.3"
  s.summary      = "地区选择器"
  s.description  = <<-DESC
                  高仿京东地区选择器
                 DESC
  s.homepage     = "https://github.com/90candy/YWChooseAddressView"
  s.license              = { :type => "MIT", :file => "LICENSE" }
  s.author             = { "90Candy" => "90candy.com@gmail.com" }
  s.social_media_url   = "https://www.jianshu.com/u/0f7d26d766f4"
  s.platform     = :ios, "9.0"
  s.source       = { :git => "https://github.com/90candy/YWChooseAddressView.git", :tag => s.version }
  s.source_files  = "YWChooseAddress/YWChooseAddressView/**/*.{h,m}"
  s.resources = "YWChooseAddress/YWChooseAddressView/Resource/*.{png,json}"
  s.requires_arc = true
  s.dependency "FMDB", "~> 2.7.2"
end
 # 验证命令：pod spec lint YWChooseAddressView.podspec --allow-warnings --verbose
 # 发布命令：pod trunk push YWChooseAddressView.podspec  --allow-warnings --verbose
```
3、验证`podspec`文件
```
pod spec lint YWChooseAddressView.podspec --allow-warnings --verbose
```
4、如果验证通过则会出现`success`or `xxx.podspec passed validation.` 字样，则可发布到`cocoapod`上

#### 发布之前需注册发布者信息：

5、注册`trunk`执行:
```
pod trunk register 90candy.com@gmail.com  "阿唯不知道"
```
6、注册之后在对应的邮箱中去点击链接激活，然后执行:
```
pod trunk me // 可查看你的注册信息包括（name、email、since、Pods、sessions）
```
7、发布操作到`CocoaPod`上
```
pod trunk push YWChooseAddressView.podspec  --allow-warnings --verbose
```
8、发布成功后显示如下：
```
 🎉  Congrats

 🚀  YWChooseAddressView (1.0.3) successfully published
 📅  January 25th, 01:32
 🌎  https://cocoapods.org/pods/YWChooseAddressView
 👍  Tell your friends!
```

9、添加其他维护者（如果你的`pod库`是由多人维护的）
```
pod trunk add-owner YWChooseAddressView yw@gmail.com
```
----
#### 无法搜索到怎么办？继续往下看
----
10、如果发布了自己的私有库无法搜索到
> 成功发布了自己的私有库以后 pod search YWChooseAddressView 无法搜索到类库的解决办法

11、先执行 ` pod setup ` 成功后会生成： 
```
~/Library/Caches/CocoaPods/search_index.jso 文件
```
12、然后执行` rm  ~/Library/Caches/CocoaPods/search_index.json ` 删除该文件：
```
~/Library/Caches/CocoaPods/search_index.json 文件删除成功后不会有任何提示，继续执行下一步即可
```
13、之后就可以直接执行命令`pod search XXX`搜索，这时候会重新生成  ：
```
~/Library/Caches/CocoaPods/search_index.json 文件
```

14、实际操作命令记录：
```
MacBook-Pro:YWChooseAddress Candy$ pod search YWChoose
[!] Unable to find a pod with name, author, summary, or description matching `YWChoose`
MacBook-Pro:YWChooseAddress Candy$ pod setup 
Setting up CocoaPods master repo
  $ /usr/local/bin/git -C /Users/apple/.cocoapods/repos/master fetch origin
  --progress
  remote: Counting objects: 105, done.        
  remote: Compressing objects: 100% (100/100), done.        
  remote: Total 105 (delta 66), reused 0 (delta 0), pack-reused 0        
  Receiving objects: 100% (105/105), 11.83 KiB | 0 bytes/s, done.
  Resolving deltas: 100% (66/66), completed with 40 local objects.
  From https://github.com/CocoaPods/Specs
     ce8f7b14f05..e643cf8834a  master     -> origin/master
  $ /usr/local/bin/git -C /Users/apple/.cocoapods/repos/master rev-parse
  --abbrev-ref HEAD
  master
  $ /usr/local/bin/git -C /Users/apple/.cocoapods/repos/master reset --hard
  origin/master
  HEAD is now at e643cf8834a [Add] LBCollectionViewLayout 1.0.4
Setup completed
MacBook-Pro:YWChooseAddress Candy$ rm  ~/Library/Caches/CocoaPods/search_index.json
MacBook-Pro:YWChooseAddress Candy$ pod search YWChooseAddressView
Creating search index for spec repo 'macdownapp'.. Done!
Creating search index for spec repo 'master'.. Done!

```


![](http://upload-images.jianshu.io/upload_images/2822163-089602958ae7072a.png)
