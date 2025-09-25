#### 第 1 步: `git add` - 将你的修改“加入购物车”

告诉 Git，你希望在下一个版本快照中包含哪些文件的修改。

Bash

```
# 如果只想添加某一个文件
git add 文件名.md
```

```
# 或者，一个更常用的命令：添加所有修改过的和新建的文件
git add .
```

_（注意，`git add` 后面有个点 `.`，代表当前目录下的所有变更。）_

#### 第 2 步: `git commit` - 为你的修改“拍照存档”

将“购物车”里的所有内容，在你的**本地仓库**里生成一个永久的版本快照。你必须为这次快照写一个清晰的注释。

Bash

```
git commit -m "这里写上有意义的注释，比如：增加了用户注册功能"
```

好的注释非常重要，比如：

- `git commit -m "Fix: Corrected a typo in the homepage title"`
    
- `git commit -m "Feat: Add user profile page"`
    

#### 第 3 步: `git push` - 将本地存档“同步到云端”

把你本地仓库里新拍的“照片”（commits），上传到 GitHub，与团队分享或作为云端备份。

Bash

```
git push
```

没错，现在只需要简单的 `git push` 即可！因为你第一次推送时用了 `-u`，Git 已经记住了应该推送到哪里。