#Git 基础操作
1. 配置用户名、邮箱
	1. git config --global user.name XXX
	2. git config --global user.email "XXX"
2. 如何将本地项目提交到远程仓库：
	1. 在github上创建仓库-->在项目目录下：git init
	2. 使用gitGUI工具菜单远端-->Add-->名字：仓库的名称、localtion远程仓库地址--Add
	3. 将本地文件内容即“已缓存的改动”，提交git管理的master分支上。
	4. 将本地文件上传到github上去。单击Git Gui界面“上传”--“上传”窗口随意勾选一个或多个传输选项--点击“上传”--上传Blog过程中需要输入github的登录账号和密码。
	5. 参考：http://jingyan.baidu.com/article/27fa732683ebf546f8271f2e.html 
3. git 提交当前目录文件到服务端：git add --all -> git commit -m "test first commint" --> git pull --> git push -u origin master
4. commit和commit -a的区别, commit -a相当于:
	1. 第一步：自动地add所有改动的代码，使得所有的开发代码都列于index file中
	2. 第二步：自动地删除那些在index file中但不在工作树中的文件
	3. 第三步：执行commit命令来提交
5. git checkout:git checkout --force 强制恢复远程分支到本地。本地所有修改都没有了
