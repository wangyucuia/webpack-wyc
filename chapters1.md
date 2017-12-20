1. 在 gitHub上创建远程公开仓库
2.在本地创建要上传的文件
3. 给本地文件 添加git 管理, 并提交到本地代码仓库
	![本地代码仓库.png]()(http://upload-images.jianshu.io/upload_images/851071-8e397626187c906b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


	cd /Users/xxx/Desktop/FM
	git init  (添加 git 管理 )
	git status (红色代表 工作区 未提交的代码)
	git add . (提交当前文件夹下的所有文件 , 把工作区的代码, 放在暂缓区)
	git status  (绿色代码 在暂缓区 代码未提交)
	git commit -m "xxx" (提交到代码仓库(本地)-m 是加注释 )

4.提交到远程代码仓库
   
git remote (查看远程代码仓库的地址)
git remote add origin https://github.com/XXX/testFM.git (origin 远程)  (https 需要 账号密码, SSH 需要 公钥和私钥)
git push origin master

5. 添加标签

	 
	git tag (查看当前的所有标签)
	git tag -a '0.01' -m '打标签'
	git push --tags(提交本地的所有标签)

6. 代码更改的时候使用

	 
	git status
	git add .
	git commit -m 'xxx'
	git log (查看当前的版本号)
	git tag -a '0.02' -m '第二个标签'
	git push origin 0.02

7. 删除某个标签

	  
	git tag -d 0.0.1   (删除的是本地的)
	git push origin :0.0.1 (删除远程的标签, : 表示删除)

	 

	 


	 




