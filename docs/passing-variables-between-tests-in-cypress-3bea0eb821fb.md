# 在 Cypress 中的测试之间传递变量

> 原文：<https://medium.com/quick-code/passing-variables-between-tests-in-cypress-3bea0eb821fb?source=collection_archive---------0----------------------->

## 使用 cy.task()测试业务工作流 end2end

![](img/2980f0e88ee570ae251349068332855d.png)

Photo by [Aaron Burden](https://unsplash.com/@aaronburden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/checklist?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当编写健壮的和隔离的测试用例时，我总是试图遵循 Cypress 的最佳实践。如果需要测试数据，我会使用夹具，这样我就可以预测结果。但是有时您想要测试整个工作流，其中不同类型的用户执行流程的不同部分。