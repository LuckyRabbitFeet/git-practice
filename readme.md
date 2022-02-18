# 常用命令

| 命令                                          | 作用                           |
| --------------------------------------------- | ------------------------------ |
| git config                                    | 修改 git 配置信息              |
| git init                                      | 在当前目录初始化 git 仓库      |
| git clone                                     | 克隆现有的仓库                 |
| git add                                       | 把文件加入暂存进行跟踪         |
| git commit                                    | 提交更新                       |
| git status                                    | 检查当前文件状态               |
| git rm                                        | 移除文件                       |
| git mv                                        | 移动文件                       |
| git branch                                    | 创建分支                       |
| git branch -u [remotename]/[branch]           | 设置已有的本地分支跟踪远程分支 |
| git branch -vv                                | 查看所有跟踪分支               |
| git checkout                                  | 切换分支                       |
| git checkout -b [branch][remotename]/[branch] | 跟踪远程分支                   |
| git merge                                     | 合并分支                       |
| git rebase | 合并分支，变基，作用：可以把当前节点作为新节点，简化提交日志 |
| git push (remote) (branch)                    | 推送远程分支                   |
| git push origin --delete [branch]             | 删除远程分支                   |
| git log --oneline --decorate --graph --all | 查看提交日志 |
| git fetch | 拉取远程仓库 |
| git rebase -i HEAD~3 | 展示最近 3 次修改，可以修改提交信息，也可以用来合并commit |

## 使用 git rm 和直接删除文件的区别

步骤

1. 新建文件`test1.txt`和`test2.txt`加入仓库
2. 使用`git rm`删除`test1.txt`；使用`rm`删除`test2.txt`
3. 确认`git status`信息

`git status`提示信息如下：

```bash
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   readme.md
        deleted:    test1.txt

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    text2.txt
```

4. 使用`git add -A`将全部修改加入暂存
5. 确认`git status`信息

`git status`提示信息如下：

```bash
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   readme.md
        deleted:    test1.txt
        deleted:    text2.txt
```

6. 执行`git commit`提交更改
7. 确认`git log`信息

`git log`提示信息如下：

```bash
Author: XXX <xxx@xxx.net>
Date:   Sun Dec 8 16:26:09 2019 +0800

    删除test1.txt和test2.txt
```

总结：根据实验可以得出，在执行`git rm`命令后会同时删除文件和保存在暂存区中的数据，而直接删除则需要手动执行一次`git add`将删除操作保存到暂存区。

对于实际业务操作来说要细分步骤，不管是否使用`git rm`命令，在删除文件后都需要进行提交留下操作记录，便于日后的维护/回退。

# 忽略文件

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为`.gitignore`的文件，列出要忽略的文件模式。 来看一个实际的例子：

```
$ cat .gitignore
*.[oa]
*~
```

第一行告诉 Git 忽略所有以`.o`或`.a`结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本。 此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的文档等等。 要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

文件`.gitignore`的格式规范如下：

- 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式可以以（/）开头防止递归。
- 匹配模式可以以（/）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]`匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如`[0-9]`表示匹配所有 0 到 9 的数字）。 使用两个星号（`*`) 表示匹配任意中间目录，比如`a/**/z`可以匹配`a/z`,`a/b/z`或`a/b/c/z`等。

# 如何避免每次输入密码

如同文档所说：

> 如果你使用的是 SSH 方式连接远端，并且设置了一个没有口令的密钥，这样就可以在不输入用户名和密码的情况下安全地传输数据。 然而，这对 HTTP 协议来说是不可能的 —— 每一个连接都是需要用户名和密码的。 这在使用双重认证的情况下会更麻烦，因为你需要输入一个随机生成并且毫无规律的 token 作为密码。

没错，如果你是使用 HTTPS 方式连接远端的话，就是需要每次都输入用户名和密码的，死心吧。

不过文档同时也给出了应对方案，可以让你不用每次都自己手动输入用户名和密码，这个应对方案有2种选择：1. 在内存中缓存账密15分钟；2. 在本地明文保存账密。

如何取舍就看实际情况和个人需求了。

相关文档： \
[4.3 服务器上的 Git - 生成 SSH 公钥](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5) \
[7.14 Git 工具 - 凭证存储](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%87%AD%E8%AF%81%E5%AD%98%E5%82%A8#_credential_caching)

# fetch 和 pull 的区别

![git](./git.jpg)

如图，fetch 在拉取后不会合并，pull 在拉取后会进行合并

# 关于 merge 和 rebase 的合并

看这些文章

[git merge和git rebase的区别, 切记：永远用rebase](https://medium.com/@neatcoding/git-merge%E5%92%8Cgit-rebase%E7%9A%84%E5%8C%BA%E5%88%AB-%E5%88%87%E8%AE%B0-%E6%B0%B8%E8%BF%9C%E7%94%A8rebase-7424eaf57dbc) \
[git rebase 和 git merge 的区别](https://www.jianshu.com/p/f23f72251abc) \
[git中merge和rebase的区别](https://gist.github.com/68681395/9d2789f38fb62e60b9fa0d0c4a7d4511)

