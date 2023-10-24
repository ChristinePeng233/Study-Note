# Git

## 一.Git初始化

初始化：`git init`

弹出：已初始化空的Git仓库于...(路径)

此时，git为隐藏目录 

## 二.Git配置

- 配置用户名称：`git config user.name "用户名"`
- 配置用户邮箱地址：`git config user.email "邮箱地址"`
- 查看本地仓库所有配置项：`git config -l`

![1](E:\study information\myself\Git\1.png)

- 删除配置：`git config --unset xxx`

  - 例如：`git config --unset user.name`、`git config --unset user.email`

- 在全部git中配置：--global`git config --global xxx xxx(具体)`

  - 例如：`git config --global user.name "pxy"`、`git config --global user.email "1024145070@qq.com"`
  - ![2](E:\study information\myself\Git\2.png)
  - 相应地，在删除时也要加上`--global`才能删掉
  - ![3](E:\study information\myself\Git\3.png)


## 三.Git基本操作

### 3.1认识工作区、暂存区/版本库

![4](E:\study information\myself\Git\4.jpg)

目前情况下，Git能否管理ReadMe文件？？

答案：不能

**.git**是版本库（仓库）

那是否可以在.git仓库下建立ReadMe？？

答案：不能

![5](E:\study information\myself\Git\5.png)

**不允许在.git下手动修改**，否则可能导致整个git报废

![6](E:\study information\myself\Git\6.jpg)

### 3.2添加文件 -- 场景一

在包含.git的目录下创建一个ReadMe文件，我们可以使用`git add` 命令可以将文件添加到暂存区：

- 添加一个或多个文件到暂存区：`git add [file1] [file2]...`
- 添加到指定目录到暂存区，包括子目录：`git add [dir]`
- 添加**当前目录下的所有文件**改动到暂存区：`git add .`
  再使用`git commit`命令将暂存区内容添加到本地仓库中
- 提交暂存区全部内容到本地仓库中：`git commit [file1] [file2] ... -m "message"

注意`git commit`后面的`-m`选项，要跟上描述本次提交的message，由用户自己完成，这部分内容绝对不能省略，并要好好描述，使用来记录你的提交细节，是给我们人看的

例如：![7](E:\study information\myself\Git\7.png)

- 显示时间从近到远的提交记录：`git log`
  ![8](E:\study information\myself\Git\8.png)
  `git log --pretty=oneline`用漂亮的方式打印一行
  ![9](E:\study information\myself\Git\9.png)

### 3.3查看.git文件

- `cat .git/HEAD`![10](E:\study information\myself\Git\10.png)指：heads指向master

- `cat .git/refs/heads/master`
  master存储的是最近提交的commit ID![11](E:\study information\myself\Git\11.png)

- `git cat-file -p commitID`
  `cat-file`查看文件内容

  -p是指让显示更整洁
  例如：![12](E:\study information\myself\Git\12.png)
  如有：parent开头，指上一次提交的commit ID

  ![13](E:\study information\myself\Git\13.png)

### 3.4添加文件 __场景二

![14](E:\study information\myself\Git\14.png)

我们的目的是把file5、file6都提交至本地仓库，为何这里只打印一个文件被修改呢？

add只将file5放入暂存区中，commit是将暂存区的内容写入到本地仓库

这里只有file4在暂存区

### 3.5Git基本操作：修改文件

Git追踪管理的其实是修改，而不是文件

`git status`:用于查看上一次提交到现在是否对文件进行修改

![15](E:\study information\myself\Git\15.png)

`git diff [file]`:命令用来显示暂存区和工作区文件的差异，显示的格式正是Unix通用的diff格式。也可以使用`git diff HEAD -- [file]`命令来查看版本库和工作区文件的区别。

---、a/:改动前 +++、b/:改动后

-1：改动前第一行内容

+1：改动后第一行内容

2：到第二行结束

![16](E:\study information\myself\Git\16.png)

![17](E:\study information\myself\Git\17.png)

![1696494675281](D:\缓存\WeChat Files\wxid_qc40skhcsj4722\FileStorage\Temp\1696494675281.png)

此时提示我们工作区已干净

### 3.6版本回退

之前我们也提到过，Git能够管理文件的历史版本，这也是版本控制器重要的能力，如果有一天你发现之前的工作出现了很大的问题，需要在某个特定的历史版本重新开始，这个时候，就需要版本回退功能了。

执行`git reset`命令用于回退版本，可以指定退回某一次提交的版本。要解释一下”回退“本质是要将版本库中的内容进行回退，工作区或暂存区是否回退由命令参数决定：

`git reset`命令语法格式为：`git reset [--soft | --mixed | --hard][HEAD]`

- `--mixed`为默认选项，使用时可以不用带该参数。该参数将暂存区的内容退回为指定提交版本内容，工作区文件保持不变。
- `--soft`参数对于工作区和暂存区的内容都不变，只是将版本库回退到某个指定版本
- `--hard`参数将暂存区与工作区都退回到指定版本。切记工作区有未提交的代码时不要用这个命令，因为工作区会回滚，你没有提交的代码就再也找不回了，所以使用该参数前一定要慎重。
- HEAD说明：
  - 可直接写成commit id，表示指定退回的版本
  - HEAD表示当前版本
  - HEAD^ 上一个版本
  - HEAD^^ 上上一个版本
- 如用--hard清理，且找不到之前的log id，可以使用`git reflog`

### 3.7撤销修改

#### 3.7.1情景一.撤销工作区中修改

模拟修改文件，在ReadMe中添加一行`xxx code`,如图：

![18](E:\study information\myself\Git\18.png)

`git checkout -- [文件名]`：**工作区**撤销修改

![19](E:\study information\myself\Git\19.png)

#### 3.7.2情景二.撤销工作区和暂存区中修改

模拟修改文件，在ReadMe中添加一行`xxx code`,如图：

![18](E:\study information\myself\Git\18.png)

`git add ReadMe`将其放入暂存区，然后用`git status`查看暂存区状态：

![20](E:\study information\myself\Git\20.png)

`git reset HEAD [文件名] `：暂存区和版本区都进行一次回退（上一个版本是HEAD^，上一个版本是HEAD^^）

![21](E:\study information\myself\Git\21.png)

### 3.8删除文件 

以删除file5为例：

1. `rm file5`在工作区中删除
2. `git add file5`工作区变动提交至暂存区
3. `git commit -m "delete file5"`提交至版本区

简化：

1. `git rm file5`在工作区和缓存区都删除
2. `git commit -m "delete file5"`

## 四.分支管理

### 4.1理解分支

### 4.2创建、切换、合并分支

### 4.3删除分支

### 4.4合并冲突

### 4.5合并模式

### 4.6分支策略

### 4.7bug 分支

### 4.8 强制删除分支

## 五.Git远程操作

### 5.1理解分布式版本控制系统

### 5.2创建远程仓库

### 5.3克隆远程仓库

#### 5.3.1HTTPS

#### 5.3.2SSH

### 5.4向远程仓库推送

### 5.5拉取远程仓库

### 5.6忽略特殊文件

## 六.标签管理

### 6.1配置命令别名

### 6.2操作标签

### 6.3推送标签

## 七.多人协作

### 7.1完成准备工作

### 7.2协作开发

### 7.3将内容合并进master

### 7.4协作开发1

### 7.5协作开发2

### 7.6将内容合并进master

## 八.git branch -a 打印已被删除的远程分支的方法

## 九.企业级开发模型

### 9.1企业级开发流程

### 9.2系统开发环境

### 9.3Git分支设计模型

### 9.4企业级项目管理

#### 9.4.1准备工作

#### 9.4.2开发场景实操









### 