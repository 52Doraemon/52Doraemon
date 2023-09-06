1、Git提交代码前两步为

* 第一步：提交（`add`）到本地
* 第二部：推送（`commit`）到远程

~~~bash
git add .  或者  git add someFile
git commit -m 'feat: xxx xxx msg'
~~~

2、**commit之后如何撤销或回滚commit操作**

如果需要撤销本次提交，例如重新修改一下，或者 commit -m 的内容需要调整

* 撤销commit命令，--soft 为默认参数，此时不删除工作空间的改动代码 ，撤销commit，不撤销add

~~~bash
git reset --soft HEAD^
~~~

* 连之前add的内容也撤销，可以使用 --hard 参数，此时删除工作空间的改动代码，撤销commit且撤销add，基本就是一切还原改动前

~~~bash
git reset --hard HEAD^
~~~

**HEAD^命令解释：**

> HEAD^ 表示上一个版本，即上一次的commit，几个 ^ 代表几次提交，如果回滚两次就是HEAD^^；
> 也可以写成HEAD~ 1，如果进行两次的commit，想要都撤回，可以使用HEAD~2；
