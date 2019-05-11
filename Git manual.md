### Git上面创建项目然后克隆到本地

```
git clone http://..git
//修改文件,新建的文件夹里要有文件
git add -A
// git status 查看更新状态
git commit -m "modifier xxx"
//第一次push要先关联github并且登陆。 git remote add origin http://..git
// git pull origin master 先pull避免冲突
git push origin master
```



###  创建本地repos

- create a repository and get the file path
```
mkdir learngit
cd learngit
pwd
```
- 修改个人信息 

```
git config --global user.name "jianghaolouis"
git config --global user.email "..."
git config --global color.ui true (change terminal color)
```

### 初始化和提交修改  

$$ working \quad repository \frac {git \quad add}{version \quad repos}  stage \frac {git \quad commit}{}  master​$$

- git init (initialize the repos)
    ls -ah (see hidden file)
- vi readme.txt (create new file) Mac中输入完成后，esc, shift + ：，然后输入 wq 保存退出 若不保存 q!
- git add readme.txt
- git commit -m "wrote a readme file"  
    为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，所以你可以多次add不同的文件 git add file2.txt         file3.txt

- git status （掌握repos状态）
- git diff (在add之前，查看之前修改掉的内容，面的忘掉了自己改了什么)
- git diff HEAD --readme.txt (查看工作区和版本库的区别)
- 每次修改之后都要 git add, git commit -m  

- git log (查看提交日志，修改记录，由近到远) or git log --pretty=oneline
- cat readme.txt (查看文件)

### 回溯版本

因为错乱修改了某个文件，或者误删了某个文件，可以从仓库中提取原来的版本

- 只是本地修改了，或者误删了文件，在提add之前：（当你改乱了工作区某个文件的内容，想直接丢弃工作区的所有修改时）
```
git checkout -- readme.txt
```
- 已经add了 但还没有commit ：（当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改）
```
git reset HEAD readme.ext   (git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。)
git checkout -- readme.txt
```
- 已经commit，加入到了本地库，但还没有推送到远程库
  HEAD 表示当前版本， 回溯上一版本 HEAD^  上上版 HEAD^^, 100版之前 HEAD～100
```
git reset --hard HEAD^
git reset --hard eee4d   (输入版本commit号码的前几位，就可以回到已经删除的版本上，只要没关闭窗口)
git reflot (如果关掉了窗口，只能从命令历史中找到删掉版本的commit号码)
```

- 如果推送的远程库， 那就等着所有人都能看到吧 ～

- git rm readme.txt (删除一个文件)

### 远程仓库 
分布式版本控制系统：找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上或者上传。
GitHub

######  GitHub 配置
- 创建ssh key. 在主目录里面输入

   
```
ssh-keygen -t rsa -C "example@example.com"
```
- GitHub账号里面， account personal setting - ssh keys, 在Key文本框里粘贴id_rsa.pub(在前面创建的.ssh文件包下) 或命令输入 open ~/.ssh 

###### 本地库推送到 新的repos 

- 在 GitHub里面创建一个 new repository, for example ,named learngit
- 关联本地仓库关联GitHub上面那个新仓库：(在本地仓库下运行) git库的默认库民名为origin

```
git remote add origin git@github.com:jianghaolouis/learngit.git 
```

   problem : Remote origin already exists on 'git push' to a new repository  
   remove the existing relation : git remote rm origin


- 把本地的master推送到远程（git push 第一次需要加上 -u 用于关联两个库）
```
git push -u origin master
```
problem:  
- ssh: connect to host github.com port 22: Connection timed out

```
git config --local -e , then change entry of 
$ url = git@github.com:username/repo.git$  to  $url = https://github.com/username/repo.git$

    vi ~/.ssh/config
    帖入保存：
    Host github.com
    Hostname ssh.github.com
    Port 443
```

- ! [rejected] master -> master (fetch first)

```
change $git push -u origin master$ to $ git push --force origin master$
```
finally, you can get the message : $Everything \quad up-to-date$

###### GitHub的repos 克隆到本地 

```
method 1 ssh 更快: git clone git@github.com:jianghaolouis/learngit.git
method 2 https协议: git clone https://github.com/jianghaolouis/learngit.git
```
转移文件时同时Add多个文件或者文件夹：
```
git add -A : stages all changes(including Adding and DELECTING )
git add . : stages new files and modifications, without deletions
git add -u : stages modifications and deletions, without new files
```



### 分支管理
HEAD指向的是当前分支。在不同的分支可以单独操作，最后再进行合并。master 主分支  branch 分支  
- 创建和切换到不同分支：(比如有个分支叫dev, -b 代表创建branch)
```
git branch dev
git checkout dev
git checkout -b dev
```
- 查看当前分支 ： git branch
- 修改完新分支后，指回master ： git checkout master
- 把指定分支合并到当前分支 ：git merge dev (dev是指定需要合并的分支，现在的HEAD指向了master)
- 删除分支 git branch -d dev

##### 当合并有多个版本而产生冲突
合并产生冲突后，在文件里面会写上多个版本. Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容.  
解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
```
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```
Solution : 修改这一部分，然后保存。 add,commit 这样多个branch就converge到一个版本了。

```
for example, 删掉这些代码然后 write as 'Creating a new branch is quick and simple'.
```
查看分支何必图：
```
git log --graph
```

##### 如何把远程修改的文件同步到本地

- git remote -v  (查看远仓版本号)
- git fetch origin master:master1 (下载远程到本地并新建master1分支)
- git diff master1
- git merge master1 (与当前版本合并)
- git branch -D master1 (删除本地分支master1)


### 小技巧
##### 忽略文件 .gitignore
把某些Git工作目录中的文件，不能提交它们，比如保存了数据库密码的配置文件，每次git status都会提醒，这时候，在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去。
###### 只对还没有加进git库的文件有效，对于git中存在的文件，需要首先删除它
```
//创建.gitignore文件
touch .gitignore
//修改
vim .gitignore

#为注释 – 将被 Git 忽略
*.a       # 忽略所有 .a 结尾的文件
!lib.a    # 但 lib.a 除外
/TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/    # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa

#my file
igonre_test.txt
```

Reference :  
[1] https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
