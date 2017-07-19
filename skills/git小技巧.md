### git一些有用的小技巧

**快速切换合并分支**
git checkout -  // 切换到最近一次分支 (git checkout A  // 切换到A分支)
git merge -     //合并最后一次分支


**git 修改用户信息**

修改全局用户信息
git config --global user.name 'xxx'
git config --global user.email 'xxx@xxx'

修改当前路径下的用户信息
git config user.name 'xxx'
git config user.email 'xxx@xxx'


** 设置global **
Git global setup
git config --global user.name "wb.zhoujing"
git config --global user.email "wb.zhoujing@mesg.corp.netease.com"

** 创建一个新的git库 **
Create a new repository
mkdir Moba-test
cd Moba-test
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin ssh://xxxxxx/xxxx/xxxxx.git
git push -u origin master

** 推一个现有的git库 **
Push an existing Git repository
cd existing_git_repo 
git remote add origin ssh://xxxxxx/xxxx/xxxxx.git
git push -u origin master