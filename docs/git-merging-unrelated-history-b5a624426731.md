# Git —合并不相关的历史

> 原文：<https://medium.easyread.co/git-merging-unrelated-history-b5a624426731?source=collection_archive---------2----------------------->

做一些可疑的事情来删除你的历史？

没事，这不是罪。事实上，这是必须要做的事情。例如:您的凭证 API 或任何相关的密码字段。

当你试图拉远改变时，请记住你必须使用这个额外的参数:

```
git pull [remote name] [branch name] --allow-unrelated-histories
```

现在，你得走了。推动你的清白历史！

请把这也告诉你的团队，因为你所做的可能会“改写”整个 git 历史。

参考资料:

*   [http://stackoverflow.com/a/40107973/3763032](http://stackoverflow.com/a/40107973/3763032)
*   https://stackoverflow.com/a/37938036/3763032
*   [https://medium . com/@ spences 10/git-allow-unrelated-histories-a39a 3814 b 981](https://medium.com/@spences10/git-allow-unrelated-histories-a39a3814b981)

***编码快乐，伙计们！***