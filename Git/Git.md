# Git

## 2.Git基础

### [2.3Git - 查看提交历史](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2) 

## 7.Git工具

### [7.6重写历史](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)

[修改commit时间](https://www.php.cn/faq/493446.html)



## 问题

1.idea中使用git，在分支上出现了叹号。代表rebase没有执行完，可以执行 git rebase  --continue / -- skip / -- abort  继续/跳过/中止 等操作来去感叹号“！”显示

![](https://typora-images-repository.oss-cn-beijing.aliyuncs.com/mall/notes/1693357941989.jpg)

```bash
PS D:\Java\Project\work\erong\ldw-tender-mer> git rebase -i HEAD~2
fatal: It seems that there is already a rebase-merge directory, and
I wonder if you are in the middle of another rebase.  If that is the
case, please try
        git rebase (--continue | --abort | --skip)
If that is not the case, please
        rm -fr ".git/rebase-merge"
and run me again.  I am stopping in case you still have something
valuable there.
```

