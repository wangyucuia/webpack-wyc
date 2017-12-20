
![][image-1]

# 创建自己的cocoapods 和spec
之前的步骤, 跟 上传到git 的步骤也一样

	cd /Users/wangyucui/Desktop/test
	pod spec create test     (create 后面的文件名, 一般跟文件夹的名字一样, 创建 test.podspec) (本地库到这一步就可以了, 不需要下面的步骤了)
	git tag (查看当前的版本号跟 podspec 里面的版本号是否一致)
	git tag '0.01' 
	git push --tags
	pod trunk register m15201386258@163.com 'wyc' --verbose  注册 trunk 
	pod trunk push test.podspec
 


# podspec 文件的介绍
	s.version 版本号
	s.summary 简介
	s.description 详细介绍, 一定要注意字数要比 s.summary长
	s.homepage 框架的地址  https://github.com/xxxx/testW  浏览器的网址
	s.license      = "MIT"
	ss.source       = { :git => "https://github.com/wangyucuia/testW.git", :tag => "#{s.version}" }  (git后面的网址是真正的代码地址, 是clone的地址)
	s.source_files   找到地址, 要下载那些文件(并不是下载所有的文件, 是下载你指定的文件)
	s.source_files  = "Classes", "Classes/**/*.{h,m}"  ** 代表的是匹配的目录, 在这文件下的子文件夹, 也可以匹配的到  * 代码的是文件
	s.exclude_files 当前用不到可以注释掉

# 本地私有库
就是一个仓库. 存储在本地, 在本地的其他工程直接使用
	cd /Users/WYC/Desktop/私有库/localLib/testW/Person/Classes
	touch Person.h Person.m

## 创建本地 git
	cd /Users/WYC/Desktop/私有库/localLib/testW/Person
	git init
	git add .
	git commit -m 'xxxx'
## 创建 pod 描述文件
	pod spec create Person
### 修改 描述文件
	s.homepage 内容可以不该
	s.source       = { :git => "", :tag => "#{s.version}" }   (git网址为空 )

## 为本地代码添加 cocoapod
	cd /Users/wyc/Desktop/私有库/localLib/test
	pod init
	pod repo (如果没有需要添加的 spec 地址, 使用下面的方法(寻找路径))
### 修改 Podfile 文件
	# platform :ios, '9.0'
	'' target 'test' do
	  use_frameworks!
	# pah 表示 到时候寻找, 会到某个路径下面, 找一个文件 Person.podspec
	    pod 'Person', :path =>'../testW/Person'
	end

![][image-2]




## 本地私有库的优化
*存在的问题*:
1. 需要手动创建 podspec 文件
2. 没有使用 git 管理, 手动创建的 git
3.  无法测试需要手动创建测试工程 (本来抽出就为了测试独立)

*解决方案一 本地私有库*

	cd /Users/wangyucui/Desktop/组件化/localLib
	pod lib create WYCBase (创建模板库)
	Would you like to include a demo application with your library? [ Yes / No ]
	 > Yes
	Which testing frameworks will you use? [ Specta / Kiwi / None ]
	 > None
	删除 WYCBase / WYCBase/Classes/ Replace.m 文件, 并替换成要测试的代码
	 cd /Users/wangyucui/Desktop/组件化/localLib/WYCBase/Example 
	pod install (进行更新代码)
*这种方法存在的问题*
1. 当工程的路径改变的时候, path 也要改变
2.  版本升级不好维护
3.  没办法多人合作开发

*解决方案二 远程私有库*
> 远程: 是让我的队友可以下载
> 私有: 只有我的队友可以下载


# cocoapods 远程私有库

逻辑:  把自己私有的代码 spec文件 -\> 自己的索引库 -\> 

> /Users/wangyucui/.cocoapods/repos/master pods 官方提供的公共的索引库

## 创建私有索引库
## 远程私有 索引库
	在 码市上创建私有  项目 https://coding.net , 并添加 Objective -C -.gitignore
	 pod repo (查看 索引库) WYCFMSpecs
	 pod repo add WYCFMBase https://git.coding.net/ayilimi/WYCFMSpecs.git (创建仓库名字, 并关联项目 地址)  (地址使用 HTTP 验证账号和密码, 使用 SSH 验证公钥和私钥)
	成功之后, 验证,  /Users/wangyucui/.cocoapods/repos/master pods/repos 里面有你新加的 库
	pod repo 查看所以库
 
## 远程项目

![][image-3]
	 cd /Users/wangyucui/Desktop/组件化/remoteLib
	pod lib create WYCFMBase (克隆模板库) 
	后面跟本地私有库一样 
	pod 'WYCFMBase', :path => '../'
	pod install


## 提交项目
	cd /Users/wangyucui/Desktop/组件化/remoteLib/WYCFMBase  (包括测试工程)
	'' 代码测试通过后, 修改 podspec ..... 
	'' 有时候 spec 文件是错的, 可以先验证
	'' open . 看是否在这个文件夹
	'' pod lib lint (lint 验证, 验证当前文件夹下的 spec 文件是否正确, 本地验证是不会验证 tag的)
	- pod spec lint (远程验证, 报错, 因为还没有 tag)
	//先把代码提交到仓库
	git status
	git add .
	git commit -m 'xxxx'
	git remote (看看跟远程仓库有没有管理)
	git remote add origin https://git.coding.net/ayilimi/WYCFMBase.git
	git push origin master  (提交到远程仓库)
	
	//打标签
	git tag  (查看当前标签)
	git tag '0.1.0' (本地, tag  的 值 必须跟 spec 里面的 tag 保持一致)
	git push --tags
	pod spec lint (验证 远程 spec 是否通过)


## 提交远程索引库
	pod repo
	pod repo push WYCFMBase2 WYCFMBase.podspec  (WYCFMBase2 私有库 )
	//成功之后. ...
	个人 -> .cocoapods -> repos -> WYCFMBase2 -> WYCFMBase ->0.1.0 -> WYCFMBase.podspec  (本地)
	去之前创建的远程私有索引库, 看有没有私有索引
	//直接检索项目 
	pod search WYCFMBase
	完结撒花, 终于成功了
	本地的 私有库, 可以随便改, 名字也可以随便改, 跟远程没关系喽~

## 使用
	安装 私有 的 podfile 写法
	pod repo  查看私有库的 url
	source 'git@git.coding.net:ayilimi/WYCFMSpecs.git'
	use_frameworks!
	
	target 'WYCFMBase_Example' do
	  pod 'WYCFMBase' 
	end
	
	
	但是这种, 有个问题, 别的 库 比较 AFN 就安装不上了
	所以公有库 的 source 也得加上
	source 'git@git.coding.net:ayilimi/WYCFMSpecs.git'
	source 'https://github.com/CocoaPods/Specs.git'
	use_frameworks!
	
	target 'WYCFMBase_Example' do
	  pod 'WYCFMBase'
	  pod 'MJExtension'
	end
	
	远程私有库, 这才是真的完结了那
	
	
	
	使用 SSH 情况 (把公钥给外界, 私钥留给自己)
	ssh -keygen 创建 公钥私钥
	Are you sure you want to continue connecting (yes/no)? yes
	然后添加公钥私钥
	Enter file in which to save the key (/Users/wangyucui/.ssh/id_rsa): 是否改变路径
	Enter passphrase (empty for no passphrase):  是否设置密码
	
	Finder -> 个人 -> .ssh -> id_rsa.pub 的内容放在 码市 -> 账号 -> SSH公钥上 
 
![][image-4]
# 远程私有库升级

`先把代码放在本地模板库 WYCBase classess 文件下`
	cd /Users/WYC/Desktop/组件化/remoteLib/WYCFMBase/Example 
	pod install
	
	cd /Users/WYC/Desktop/组件化/remoteLib/WYCFMBase
	git status
	修改 本地模板库里面的spec文件的版本号
	git add .
	git commit -m 'xxxx'
	git push origin master
	
	## 修改 spec 文件
	验证远程
	pod spec lint 
	git tag '0.2.0'
	git push --tags
	pod spec lint (现在验证远程可以通过)
	##本地私有索引库, 远程私有索引库
	pod repo 
	pod repo push WYCFMBase2 WYCFMBase.podspec  (可自动推送到远程)
	cd ~
	pod update --no-repo-update ( --no-repo-update 没有必要更新本地的索引库)
 

## 远程私有依赖库 + 升级
	spec文件底部添加 (会自动下载依赖库)
	 s.dependency 'AFNetworking'
	
	cd /Users/WYC/Desktop/组件化/remoteLib/WYCFMBase 
	git status
	git add .
	git commit -m 'xxx'
	git push origin master
	pod spec lint (如果有警告)
	pod spec lint --allow-warnings
	pod repo 
	pod repo push WYCFMBase2 WYCFMBase.podspec --allow-warnings

## 远程 本地私有库  分支
> 假如另一个业务线, 仅仅需要基础组件的一些基础配置, 如果把整个库都下载下来, 不太合适 
 
### 分支:
	只需要修改 podspec 文件即可, 
	1. 分支不需要 s.source_files
	2.s.subspec 'Base' do |b|   (subspec :分支, Base: 分支名 b: 小名)
	    b.source_files = 'WYCFMBase/Classes/Base/**/*'
	  end
	3. 当某个分支使用 比如 'AFNetworking' 
	s.subspec 'Category' do |c|
	    c.source_files = 'WYCFMBase/Classes/Category/**/*'
	    c.dependency 'AFNetworking'
	  end
	
	4.  # s.dependency 'AFNetworking'
	
	步骤:
	1. 修改 spec 文件
	2. git status
	3. git add .
	4, git commit -m 'xxx'
	5. git push origin master
	6. git tag '0.4.0'
	7.git push --tags
	8.pod repo
	9.pod repo add WYCFMBase2 WYCFMBase.podspec
	10. pod search WYCFMBase (可查看分支)
	11.修改 podfile 文件
	 pod 'WYCFMBase/Base' (当使用 pod 'WYCFMBase' 全部下载) (这种至下周分支)
	12.pod update
	
	
	
	git Clone
	cd ~
	git Clone 网址

 
 

## 本地私有库  改名字
	pod repo remote WYCFMBase2 (先干掉)
	pod repo add WYFMBase git@git.coding.net:ayilimi/WYCFMSpecs.git (可直接推送到远程)
## 远程私有库 里面的 xib, 以及图片展示
### xib
	//拿到 mainBundle,
	    NSBundle *mainBundle = [NSBundle mainBundle];
	    //当前 self 下的 bundle
	    NSBundle *currentBundle = [NSBundle bundleForClass:self]; 
	所以 在 xib 下使用 第二种方式
 
### 图片
远程私有库的图片 应该 放在 pod 里面, 这样安装的时候就不需要 在 重新下载图片了
组件的图片应该放在组件里面. 这样才可以找的到

`1.怎么样从 car 里面把图片整出来`
github  搜索  cartool  下载
1.![][image-5]
 2. ![][image-6]
 3.这时候, 直接 pod install 是没用的
4.修改 podspec 文件, 因为可能有 jpg , 或者音频其他资源 所有直接吧 .png 取消即可

	 s.resource_bundles = {
	    'WYCFMBase' => ['WYCFMBase/Assets/*']
	   }
 5. pod install 这样就可以引进 资源了
6.  修改 pod 里面的 图片路径
	*1. xid 情况下*
	 图片文件包的路径 + 图片名
	*2.使用代码的情况*
	imageNamed: 这个方法加载 的是 从 mainBundle 里面或者 car 里面加载的
	![][image-7]

## 组件扩展  统一披露 API

  参考原则:
*1.需要那些方法给外界*
直接以普通方法给外界即可
*2.外界需要那些服务给你*
让外界 以block的形式, 或者通知的形式传递到组件内部, 组件 	


### 删除 tag 
	git tag -d 0.1.0 (本地, 先删本地, 在删远程)
	git push origin :0.1.0 (: 远程 tag 删除)
### 清 pod 缓存
`pod clearn`

# 组件化
>   将单一工程的项目, 分解成各个独立的组件, 然后以某种方式, 任意组织成一个拥有完整业务逻辑的工程





[image-1]:	http://upload-images.jianshu.io/upload%5C_images/851071-b4f091db08da01e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[image-2]:	http://upload-images.jianshu.io/upload_images/851071-95fcb1f0153384f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[image-3]:	http://upload-images.jianshu.io/upload_images/851071-46290bf164179749.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[image-4]:	http://upload-images.jianshu.io/upload_images/851071-1590e4ebef8ff07b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700
[image-5]:	http://upload-images.jianshu.io/upload_images/851071-2ad17e62aff16237.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[image-6]:	http://upload-images.jianshu.io/upload_images/851071-cf5e8293537aa3ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[image-7]:	http://upload-images.jianshu.io/upload_images/851071-8f28f009b52d9ee6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240