## Git

#### 1. commit 提交

```
git commit 
```

> 在当前分支提交

#### 2. branch 创建分支

```
git branch <name>
```

> 如果创建了分支不在当前分支下，即使后面 git commit 提交的也是当前分支 新分支不动

#### 3. 切换分支

```
git checkout <name>
```

#### 4.分支与合并

**在 git 中合并两个分支会产生一个特殊的提交记录，它有两个父节点，既要把两个父节点本身及他们所有的祖先都包含进来**

```
// 要把 bugFix 合并到 main 中
// 当前分支是 main
git merge bugFix
```



##### 案例：

! [image-20210114185743908](/Users/h1/Library/Application Support/typora-user-images/image-20210114185743908.png)

! [image-20210114185629204](/Users/h1/Library/Application Support/typora-user-images/image-20210114185629204.png)



```
git branch bugFix
git checkout bugFix
git commit
git checkout main
git commit
git merge bugFix
```



#### 5. 合并分支（第二种）

**实际上就是取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去**

```
//把 bugFix 分支里的工作直接移到 main 分支上
git rebase
```

> 我们想要把 bugFix 分支里的工作直接移到 main 分支上。移动以后会使得两个分支的功能看起来像是按顺序开发，但实际上它们是并行开发的。
>
> `git rebase main`

![image-20210114192335575](/Users/h1/Library/Application Support/typora-user-images/image-20210114192335575.png)

```
$ git checkout -b bugFix    包含了 checkout & commit
$ git commit
$ git checkout main
$ git commit
$ git checkout bugFix
$ git rebase main
```



#### 6.分离HEAD

##### 在提交树上移动

> HEAD 是一个对当前检出记录的符号引用 —— 也就是指向你正在其基础上进行工作的提交记录。
>
>HEAD 总是指向当前分支上最近一次提交记录。大多数修改提交树的 Git 命令都是从改变 HEAD 的指向开始的。
>
>HEAD 通常情况下是指向分支名的（如 bugFix）。在你提交时，改变了 bugFix 的状态，这一变化通过 HEAD 变得可见。

- 查看 HEAD 指向:

  ````
  cat .git/HEAD
  //or
  git symbolic-ref HEAD
  ````

- 第一种

  ```
  git checkout (C1)
  //这里的C1指的是练习当中的 提交记录的哈希值
  ```

- 第二种 相对引用
  - 使用 **`^`** 向上移动1个提交记录
  - 使用 **～<num>** 向上移动多个提交记录，如 ～3

```
// main^ 相当于 main 的父节点
// main^^ 是 main 的第二个父节点

//切换到 main 的父节点
git checkout main^

// ☑️
git checkout HEAD^
```

- 第三种 
  - “～” 操作符 后面可以跟一个数字（可选，若没有就是向上移动一次）指定向上移动多少次

> 相对引用最多的就是移动分支 可以直接使用 -f 选项让分支指向另一个提交

> 将 main 分支强制指向 HEAD 的第三级父提交
>
> `git branch -f main HEAD~3`



#### 7.撤销变更

> 撤销变更由底层部分（暂存区的独立文件或者片段）和上层部分（变更到底是通过哪种方式被撤销）组成
>
> **这里看重后者**

- `git reset `

  对原先的提交就无效了 直接回退

  ```
  git reset HEAD^
  ```

  **! 对远程分支无效**

- `git revert`

  要撤销的提交记录后面多了一个提交，这是因为新提交记录引入了一些更改，这些更改刚好是用来撤销先前的那个提交

  ```
  git revert HEAD
  ```

  

#### 8.整理提交记录

- 如果你想将一些提交复制到当前所在位置（HEAD）下面的话，使用 `cherry-pick`

```
git cherry-pick <提交号>...
```

![image-20210115092653168](/Users/h1/Library/Application Support/typora-user-images/image-20210115092653168.png)



#### 9.交互式的rebase

> 交互式 `rebase` 指的是使用带参数 --interactive 的 `rebase` 命令，简写为**`-i`**

> 如果你在命令后增加了这个选项, Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。

当 rebase UI界面打开时, 你能做3件事:

- 调整提交记录的顺序（通过鼠标拖放来完成）
- 删除你不想要的提交（通过切换 `pick` 的状态来完成，关闭就意味着你不想要这个提交记录）
- 合并提交。 遗憾的是由于某种逻辑的原因，我们的课程不支持此功能，因此我不会详细介绍这个操作。简而言之，它允许你把多个提交记录合并成一个。



#### 10.只取一个提交记录

> 来看一个在开发中经常会遇到的情况：我正在解决某个特别棘手的 Bug，为了便于调试而在代码中添加了一些调试命令并向控制台打印了一些信息。
>
> 这些调试和打印语句都在它们各自的提交记录里。最后我终于找到了造成这个 Bug 的根本原因，解决掉以后觉得沾沾自喜！
>
> 最后就差把 `bugFix` 分支里的工作合并回 `main` 分支了。你可以选择通过 fast-forward 快速合并到 `main` 分支上，但这样的话 `main` 分支就会包含我这些调试语句了。

实际上，只要复制提交解决问题的那一个提交记录就可以了

- `git rebase -i`
- `git cherry-pick`

 #### 11. 提交的技巧#1

- 案例

  你之前在 `newImage` 分支上进行了一次提交，然后又基于它创建了 `caption` 分支，然后又提交了一次。

  此时你想对的某个以前的提交记录进行一些小小的调整。比如设计师想修改一下 `newImage` 中图片的分辨率，尽管那个提交记录并不是最新的了。

  我们可以通过下面的方法来克服困难：

  - 先用 `git rebase -i` 将提交重新排序，然后把我们想要修改的提交记录挪到最前
  - 然后用 `git commit --amend` 来进行一些小修改
  - 接着再用 `git rebase -i` 来将他们调回原来的顺序
  - 最后我们把 main 移到修改的最前端（用你自己喜欢的方法），就大功告成啦！

  当然完成这个任务的方法不止上面提到的一种（我知道你在看 cherry-pick 啦），之后我们会多点关注这些技巧啦，但现在暂时只专注上面这种方法。 最后有必要说明一下目标状态中的那几个`'` —— 我们把这个提交移动了两次，每移动一次会产生一个 `'`；而 C2 上多出来的那个是我们在使用了 amend 参数提交时产生的，所以最终结果就是这样了。

  也就是说，我在对比结果的时候只会对比提交树的结构，对于 `'` 的数量上的不同，并不纳入对比范围内。只要你的 `main`分支结构与目标结构相同，我就算你通过。

  初始化：

  ![image-20210118104100882](/Users/h1/Library/Application Support/typora-user-images/image-20210118104100882.png)

  目标

  ![image-20210118104036855](/Users/h1/Library/Application Support/typora-user-images/image-20210118104036855.png)

  

```
// 调整提交记录顺序
git rebase main 
//在交互式界面移动 C2 C3 位置
//提交 c2
git commit --amend 
//再次调整提交顺序
git rebase -i main 
git checkout main
git merge caption 
```



#### 12. 提交的技巧#2

`11`中要进行两次排序有可能造成由rebase而导致的冲突 所以使用到 `cherry-pick`

>  `cherry-pick` 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）。

```
//我的答案
git checkout main
git cherry-pick c2
git branch -f main HEAD^
git cherry-pick c2 c3
```



#### 13. git Tags

> 有没有什么可以*永远*指向某个提交记录的标识呢，比如软件发布新的大版本，或者是修正一些重要的 Bug 或是增加了某些新特性，有没有比分支更好的可以永远指向这些提交的方法呢？

**Git 的 tag 就是干这个用的啊，它们可以（在某种程度上 —— 因为标签可以被删除后重新在另外一个位置创建同名的标签）永久地将某个特定的提交命名为里程碑，然后就可以像分支一样引用了。更难得的是，它们并不会随着新的提交而移动。你也不能检出到某个标签上面进行修改提交，它就像是提交树上的一个锚点，标识了某个特定的位置。**

```
// 建立一个标签，指向提交记录 c1 ，表示这是我们的1.0版本
git tag v1 c1
```



#### 14. git describe

> 由于标签在代码库中起着“锚点”的作用，Git 还为此专门设计了一个命令用来**描述**离你最近的锚点（也就是标签），它就是 `git describe`！

>Git Describe 能帮你在提交历史中移动了多次以后找到方向；当你用 `git bisect`（一个查找产生 Bug 的提交记录的指令）找到某个提交记录时，或者是当你坐在你那刚刚度假回来的同事的电脑前时， 可能会用到这个命令。

- 语法

  ```
  git describe <ref>
  ```

  `<ref>` 可以是任何能被 Git 识别成提交记录的引用，如果你没有指定的话，Git 会以你目前所检出的位置（`HEAD`）。

  它输出的结果是这样的：

  ```
  <tag>_<numCommits>_g<hash>
  ```

  `tag` 表示的是离 `ref` 最近的标签， `numCommits` 是表示这个 `ref` 与 `tag` 相差有多少个提交记录， `hash` 表示的是你所给定的 `ref` 所表示的提交记录哈希值的前几位。

  当 `ref` 提交记录上有某个标签时，则只输出标签名称

  

#### 15. 多分支 rebase

```
//把 bugFix 合并提交在 main 的分支上(先切换到bugFix上然后再合并回来)
//HEAD指向bugFix
git rebase main bugFix
```

#### 16.选择父提交记录

操作符 `^` 与 `~` 符一样，后面也可以跟一个数字。

但是该操作符后面的数字与 `~` 后面的不同，并不是用来指定向上返回几代，而是指定合并提交记录的某个父提交。还记得前面提到过的一个合并提交有两个父提交吧，所以遇到这样的节点时该选择哪条路径就不是很清晰了。

Git 默认选择合并提交的“第一个”父提交，在操作符 `^` 后跟一个数字可以改变这一默认行为。

```
//选择上一级的右父的上两级
git checkout HEAD~^2~2
```



#### 遠程分支

- 克隆倉庫

  ```
  // 複製了一個 o/【name】的倉庫
  git clone
  ```

  **远程分支有一个命名规范 —— 它们的格式是:**

  - `<remote name>/<branch name>`

  因此，如果你看到一个名为 `o/main` 的分支，那么这个分支就叫 `main`，远程仓库的名称就是 `o`。

  大多数的开发人员会将它们主要的远程仓库命名为 `origin`，并不是 `o`。这是因为当你用 `git clone` 某个仓库时，Git 已经帮你把远程仓库的名称设置为 `origin` 了

> 远程分支反映了远程仓库(在你上次和它通信时)的**状态**。这会有助于你理解本地的工作与公共工作的差别 —— 这是你与别人分享工作成果前至关重要的一步.

> 远程分支有一个特别的属性，在你检出时自动进入分离 HEAD 状态。Git 这么做是出于不能直接在这些分支上进行操作的原因, 你必须在别的地方完成你的工作, （更新了远程分支之后）再用远程分享你的工作成果。

- 從遠程倉庫獲取數據

  ```
  // 從遠程倉庫獲取數據時，遠程分支也會更新以反應最新的遠程倉庫
  git fetch
  ```

  > `git fetch` 完成了仅有的但是很重要的两步:
  >
  > - 从远程仓库下载本地仓库中缺失的提交记录
  > - 更新远程分支指针(如 `o/main`)

**`git fetch` 并不会改变你本地仓库的状态。它不会更新你的 `main` 分支，也不会修改你磁盘上的文件。**

- git pull

  **`git pull` 就是` git fetch` 和 `git merge`的縮寫**

- 提交代碼到遠程倉庫

  ```
  git push  <远程仓库>  <本地提交分支>
  ```

  

还有其它的方法可以在远程仓库变更了以后更新我的工作吗? 当然有，我们还可以使用 `merge`

尽管 `git merge` 不会移动你的工作（它会创建新的合并提交），但是它会告诉 Git 你已经合并了远程仓库的所有变更。这是因为远程分支现在是你本地分支的祖先，也就是说你的提交已经包含了远程分支的所有变化。

**前面已经介绍过 `git pull` 就是 fetch 和 merge 的简写，类似的 `git pull --rebase` 就是 fetch 和 rebase 的简写！**



**远程仓库已更新 本地仓库没有更新**

```
git fetch;git rebase o/main;git push;
git fetch;git merge o/main;git push;
```

- 远程服务器拒绝（ Remote Rejected )

如果你是在一个大的合作团队中工作, 很可能是master被锁定了, 需要一些Pull Request流程来合并修改。如果你直接提交(commit)到本地master, 然后试图推送(push)修改, 你将会收到这样类似的信息:

```
 ! [远程服务器拒绝] main -> main (TF402455: 不允许推送(push)这个分支; 你必须使用pull request来更新这个分支.)
```

远程服务器拒绝直接推送(push)提交到master, 因为策略配置要求 pull requests 来提交更新.

你应该按照流程,新建一个分支, 推送(push)这个分支并申请pull request,但是你忘记并直接提交给了master.现在你卡住并且无法推送你的更新.



```
//本程序中默认的行为是 --hard 硬重置，可以尽情省略掉那个选项以避免麻烦 但是要记录 git 中默认的是 --mixed
git reset --hard o/main
//强制切换并创建分支 feacture 在 C2 上 并（*）在这上面
git checkout -b feacture C2
git push origin feature
```



- rebase 优缺点

  优点:

  - Rebase 使你的提交树变得很干净, 所有的提交都在一条线上

  缺点:

  - Rebase 修改了提交树的历史

  比如, 提交 C1 可以被 rebase 到 C3 之后。这看起来 C1 中的工作是在 C3 之后进行的，但实际上是在 C3 之前。



- 远程跟踪

  `main`和`o/main` 的关联关系是由分支的‘’ remote tracking ‘’属性决定的。`main`被设定为跟踪`o/main`（当你克隆仓库的时候 git 就自动帮你把这个属性设置好了）

  ```
  local branch "main" set to track remote branch "o/main"
  ```

  是否能自己指定这个属性？

  ```
  //就可以创建一个名为 totallyNotMain 分支跟踪远程分支 o/main
  git checkout -b totallyNotMain o/main
  
  
  
  //for example
  
  //main 没有被更新
  git checkout -b foo o/main;git pull
  
  
  //我们将一个并不叫 main 的分支上的工作推送到了远程仓库中的 main 分支上
  git checkout -b o/main;git commit;git push
  ```

   第二种方法

  ```
  git branch -u
  
  git branch -u o/main foo
  //如果当前在foo 分支上还能省略foo
  ```

  

- push 的参数

  ```
  git push <remote> <place>
  
  
  //切到本地仓库的 main 分支上，获取所有的提交，再到远程仓库 origin 中找到 main 分支，将远程仓库中没有的提交记录都添加上去 
  git push origin main
  
  ```

  > 我们通过“place”参数来告诉 Git 提交记录来自于 main, 要推送到远程仓库中的 main。它实际就是要同步的两个仓库的位置。
  >
  > 需要注意的是，因为我们通过指定参数告诉了 Git 所有它需要的信息, 所以它就忽略了我们所检出的分支的属性！

- 如果来源和去向分支的名称不同呢？比如你想把本地的 `foo` 分支推送到远程仓库中的 `bar` 分支。

  ```
  git push origin <source>:<destination>
  ```

  例如：

  ```
  git push origin foo^:main
  ```

- fetch 参数

  和`push`相似 

  ```
  //Git 会到远程仓库的 foo 分支上，然后获取所有本地不存在的提交，放到本地的 o/foo 上。
  
  git fetch origin foo
  ```

  **这里有一点是需要注意的 —— `source` 现在指的是远程仓库中的位置，而 `<destination>` 才是要放置提交的本地仓库的位置。它与 git push 刚好相反，这是可以讲的通的，因为我们在往相反的方向传送数据。**

**!!!`source`可以为空**

```
//通过 push 传空值 source 成功删除了远程仓库的 foo 分支
git push origin :foo
```

```
//会在本地创建一个新的分支
git fetch origin :bar
```



- pull

  ```js
  git pull orgin foo
  //相当于
  git fetch origin foo;git merge o/foo
  ```

  ```js
  git pull origin bar~1:bugFix
  //相当于
  git fetch origin bar~1:bugFix;git merge buugFix
  ```


- 创建并切换分支

  ```js
  git checkout -b branchname
  ```

- 新建一个分支，但不切换

  ```js
  git branch -f branchname
  ```



#### 选项

**-d** --delete：删除

**-D** --delete --force的快捷键

**-f** --force：强制

**-m** --move：移动或重命名

**-M** --move --force的快捷键

**-r** --remote：远程

**-a** --all：所有


#### 🌟🌟🌟🌟 推荐 
平日里练习😁
> [参考连接:https://learngitbranching.js.org/?locale=zh_CN](https://learngitbranching.js.org/?locale=zh_CN)

  

